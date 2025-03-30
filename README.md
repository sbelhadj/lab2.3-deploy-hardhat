### Lab: Déploiement de Smart Contracts avec Hardhat (Ethers.js) - Instant Payment Hub

#### Description du Lab

Dans ce lab, vous allez apprendre à déployer un smart contract de paiement instantané (InstantPaymentHub) sur le testnet Sepolia en utilisant Hardhat et Ethers.js. Vous allez également interagir avec le contrat via Ethers.js pour effectuer des paiements et valider son fonctionnement.

----------

### Prérequis

Avant de commencer ce lab, assurez-vous d'avoir configuré votre environnement comme suit :

-   Node.js >= 16.x  
      
    
-   Metamask ou un autre wallet Ethereum installé dans votre navigateur  
      
    
-   Compte Ethereum sur Sepolia Testnet  
      
    
-   GitHub Codespaces ou un IDE local avec Hardhat et Ethers.js installés  
      
    
-   Infura/Alchemy pour interagir avec Sepolia Testnet  
      
    

----------

### Objectifs du Lab

1.  Créer un contrat InstantPaymentHub en solidity.  
      
    
2.  Déployer ce contrat sur le testnet Sepolia via Hardhat.  
      
    
3.  Interagir avec le contrat via Ethers.js pour effectuer des paiements.  
      
    
4.  Vérifier les transactions et l'état du contrat via Etherscan.  
      
    

----------

### Étapes du Lab

#### 1. Préparer l'environnement

Installer les dépendances en utilisant npm :  
  
```bash  
cd lab-instant-payment-hub

npm install

```

1.  Vérifier la configuration de Hardhat :  
      
    

-   Assurez-vous que vous avez le fichier hardhat.config.js configuré pour Sepolia comme suit :  
      
    

```javascript  
require('@nomiclabs/hardhat-ethers');

  

module.exports = {

solidity: "0.8.19", // Dernière version de solidity

networks: {

sepolia: {

url: `https://sepolia.infura.io/v3/YOUR_INFURA_KEY`,

accounts: [`0x${YOUR_PRIVATE_KEY}`],

},

},

};

```

2.  Vérification de la configuration Metamask :  
      
    

-   Assurez-vous que Metamask est connecté au testnet Sepolia avec un solde suffisant pour déployer un contrat.  
      
    

----------

#### 2. Créer le contrat solidity - InstantPaymentHub

1.  Créez un fichier contracts/InstantPaymentHub.sol dans le répertoire contracts/.  
      
    
2.  Implémentez le contrat de paiement instantané :  
      
    

```solidity

// SPDX-License-Identifier: MIT

pragma solidity ^0.8.20;

  

contract InstantPaymentHub {

address public owner;

mapping(address => uint256) public balances;

  

event PaymentMade(address indexed sender, address indexed receiver, uint256 amount);

  

modifier onlyOwner() {

require(msg.sender == owner, "Only the owner can execute this");

_;

}

  

constructor() {

owner = msg.sender;

}

  

// Déposer de l'ether dans le contrat

function deposit() public payable {

balances[msg.sender] += msg.value;

}

  

// Effectuer un paiement instantané entre utilisateurs

function instantPayment(address recipient, uint256 amount) public {

require(balances[msg.sender] >= amount, "Insufficient balance");

balances[msg.sender] -= amount;

balances[recipient] += amount;

emit PaymentMade(msg.sender, recipient, amount);

}

  

// Retirer des fonds du contrat

function withdraw(uint256 amount) public {

require(balances[msg.sender] >= amount, "Insufficient balance");

payable(msg.sender).transfer(amount);

balances[msg.sender] -= amount;

}

}

```

----------

#### 3. Déployer le contrat sur Sepolia

1.  Créer un script de déploiement dans scripts/deploy.js :  
      
    

```javascript

async function main() {

const [deployer] = await ethers.getSigners();

console.log("Déployé par : ", deployer.address);

  

const Contract = await ethers.getContractFactory("InstantPaymentHub");

const contract = await Contract.deploy();

console.log("Contrat déployé à l'adresse : ", contract.address);

}

  

main()

.then(() => process.exit(0))

.catch((error) => {

console.error(error);

process.exit(1);

});

```

2.  Configurer les informations de testnet Sepolia dans hardhat.config.js comme mentionné précédemment.  
      
    

Exécuter le script de déploiement :  
  
```bash  
npx hardhat run scripts/deploy.js --network sepolia

```

3.  Cela déploiera le contrat sur le testnet Sepolia et vous donnera l'adresse du contrat déployé.  
      
    

----------

#### 4. Interagir avec le contrat via Ethers.js

1.  Créez un fichier scripts/interact.js pour interagir avec le contrat déployé via Ethers.js :  
      
    

```javascript

async function main() {

const [deployer] = await ethers.getSigners();

const contractAddress = "VOTRE_ADRESSE_DE_CONTRAT"; // Remplacez par l'adresse du contrat déployé

const contract = await ethers.getContractAt("InstantPaymentHub", contractAddress);

  

// Déposer de l'ether dans le contrat

const depositTx = await contract.deposit({ value: ethers.utils.parseEther("1.0") }); // Déposer 1 Ether

await depositTx.wait();

console.log("1 Ether déposé dans le contrat");

  

// Effectuer un paiement instantané

const recipient = "ADRESSE_D_UN_UTILISATEUR"; // Remplacez par l'adresse du destinataire

const paymentTx = await contract.instantPayment(recipient, ethers.utils.parseEther("0.5"));

await paymentTx.wait();

console.log("0.5 Ether payé à", recipient);

  

// Retirer de l'ether du contrat

const withdrawTx = await contract.withdraw(ethers.utils.parseEther("0.2"));

await withdrawTx.wait();

console.log("0.2 Ether retiré du contrat");

}

  

main()

.then(() => process.exit(0))

.catch((error) => {

console.error(error);

process.exit(1);

});

```

  

Exécuter le script d'interaction :  
  
```bash  
npx hardhat run scripts/interact.js --network sepolia

```

2.  Cela interagira avec le contrat déployé en déposant des ethers, en effectuant un paiement instantané, et en retirant des ethers.  
      
    

----------

#### 5. Vérification des résultats sur Sepolia via Etherscan

-   Une fois que le contrat est déployé et que les interactions sont effectuées, vous pouvez vérifier les résultats sur Sepolia Etherscan.  
      
    
-   Recherchez l'adresse du contrat déployé et consultez les transactions pour vérifier les paiements effectués.  
      
    

----------

  

### Fichiers du Projet

Voici un aperçu des fichiers et dossiers dans le repo pour ce lab :

```lua

lab-instant-payment-hub/

│

├── contracts/

│ └── InstantPaymentHub.sol

│

├── scripts/

│ ├── deploy.js

│ └── interact.js

│

├── test/

│ └── instant-payment-hub.test.js (optionnel pour les tests unitaires)

│

├── hardhat.config.js

├── package.json

├── README.md

```

----------

### Objectifs de l'étudiant après ce lab

-   Créer et déployer un smart contract simple de paiement instantané sur Sepolia.  
      
    
-   Interagir avec le contrat via Ethers.js pour effectuer des paiements et des retraits.  
      
    
-   Vérifier les transactions via Etherscan et comprendre comment elles sont enregistrées sur la blockchain.  
      
    

----------
