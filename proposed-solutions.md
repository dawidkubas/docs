# Proposed Solutions

- [Models](#models)
    - [Publishing model scheme](#models-schemes-publishing)
    - [Retrieving model scehme](#models-schemes-retrieving)
    - [Publishing model instance](#models-publishing)
    - [Storing model instance](#models-storing)
    - [Previewing model instance](#models-previewing)
    - [Purchasing model instance](#models-purchasing)
- [Tokenomics](#tokenomics)
    - [Publishing tokenomics scheme](#tokenomics-schemes-publishing)
    - [Retrieving tokenomics scehme](#tokenomics-schemes-retrieving)
    - [Publishing tokenomics instance](#tokenomics-publishing)
    - [Storing tokenomics instance](#tokenomics-storing)
    - [Previewing tokenomics instance](#tokenomics-previewing)
- [Simulations](#simulations)
    - [Performing simulation](#simulations-performing)
    - [Publishing simulation results](#simulations-publishing)
    - [Storing simulation results](#simulations-storing)
    - [Previewing simulation results](#simulations-previewing)
    - [Purchasing simulation results](#simulations-purchasing)
- [Analysis Reports](#analysis-reports)
- [Lifetime Events](#lifetime-events)
- [Rewarding](#rewarding)
    - [Rewarding model providers](#rewarding-model-providers)
    - [Rewarding tokenomics providers](#rewarding-tokenomics-providers)
    - [Rewarding simulation operators](#rewarding-simulation-operators)
    - [Rewarding analysts](#rewarding-analysts)
    - [Rewarding storage providers](#rewarding-storage-providers)
    - [Rewarding processing providers](#rewarding-processing-providers)
    - [Rewarding TPRO](#rewarding-tpro)
    - [Further incentivization](#further-incentivization)
- [Proofs](#proofs)
    - [Proof of Offer (PoO)](#proofs-poo)
    - [Proof of Storage (PoS)](#proofs-pos)
    - [Proof of Purchase (PoP)](#proofs-pop)
    - [Proof of Simulation (PoSIM)](#proofs-posim)
    
<a name="introduction"></a>
## Introduction

This section outlines proposed solutions for implementing the business requirements of the TPRO network. This section, similarly to design considerations, tries to stay as high-level as possible, so it will not go into details on how to achieve mentioned solutions and/or what kind of tech to use, but rather how it should work on a functional level and how it should behave. This section is further divided into sub-sections, each dedicated to its own topic.

<a name="models"></a>
## Models

Models are structures used for modeling token ecoconomic’s supply, demand and environment. Models need to be divided into two substructures: Model schemes (MS) and Model instances (MI). Schemes are structures that define the format of the model compatible with TPRO network, while Instances are schemes filled with actual data. 

Model schemes are administered only by the TPRO network itself in order to establish protocol rules and it may be possible for more than one scheme to exist at time. Model schemes are also not monetized as it is part of the protocol, meaning the access to it should be public and unencrypted. Since there is no monetization involved with schemes, the solution will require TPRO network to run a service that enables querying of the model schemes directly from TPRO network, not storage providers.

Model instances can be created by modelers, which are types of end-users. Model instances can be created in online and offline modes using TPRO application (or TPRO marketplace). Model instances contain specific data provided by the modeler, therefore in contrast to schemes, they need to be monetized. Monetization of models comes in two ways. Purchasing model directly from storage provider for local storage / other needs and purchasing model for use in simulation. Purchasing models directly from storage providers would happen between consumer and storage provider through dedicated proof. Purchasing models for simulation needs would be made indirectly through (TPRO simulation suite) in such a way simulation operator pays for simulation to TPRO, then TPRO delegates part of the payment to storage provider in order to access the model.

Due to the monetized nature of model instances, they need to be fully encrypted while stored within a storage provider, so storage provider does not have free access to the contents of the data (it is not able to workaround TPRO payments). Decryption of the data should be possible only by the original owner (modeler) or TPRO marketplace itself (while providing proof of purchase). Additionally, there needs to exist some kind of preview of models, that would contain unecrypted part of the original model in order to enable consumers to view it and use that viewing as decision maker whether to make or not make purchase (similar to how parts of PDF books are provided for free to incentivize potential buyer).

In relation to models, there needs to exist a ways for:
- TPRO (T) to publish model scheme
- User (USR) to retrieve model scheme
- User (USR) to publish model instance
- Storage Provider (SP) to store model instance
- User (USR) to preview model instance
- User (USR) to purchase model instance 

<a name="models-scheme-publishing"></a>
### Publishing Model Scheme

Publishing model scheme is a process defined as follows:
- TPRO (T) defines a new model scheme (MS)
- TPRO (T) activates a new model scheme (MS), computes its hash and publishes its contents in Ref. Data Repository (RDR)
    - Ref. Data Repository (RDR) publishes model scheme under unique API link, consisting of model scheme (MS) hash, which returns model scheme (MS) definition
- TPRO (T) registers hash of newly defined model scheme (MS) within Model Scheme Registry (MSRC) contract available on TPRO network
    - Model Scheme Registry (MSRC) contract emits event indicating registration of model scheme (MS)
    - Event is publicly visible and can be used by User (USR) to notice new model scheme and get its  routing information

<a name="models-scheme-retrieving"></a>
### Retrieving Model Scheme

Retrieving model scheme is a process defined as follows:
- User (USR) wants to retrieve a model scheme (MS)
- User (USR) uses their wallet to query model scheme routing information from Model Scheme Registry (MSRC) 
- User (USR) uses their wallet and receives routing information to query model scheme (MS) from Ref. Data Repository (RDR)
    - User (USR) receives model scheme (MS)


<a name="models-publishing"></a>
### Publishing Model Instance

Publishing model instance is a process defined as follows:
- User (USR) retrieves model scheme according to flow 3.1.2
- User (USR) uses TPRO app (TAPP) in online or offline mode to fill model scheme (MS) therefore creating a new model instance (MI)
    - User (USR) encrypts model instance (MI) and creates a preview using TPRO app (TAPP)
- User (USR) publishes model instance (MI) through TPRO marketplace (TMAR)
    - User (USR) uses their wallet to provide Proof of Publishing (PoP) to TPRO marketplace (TMAR) that contains:
        - information identifying created model instance (MI)
        - publishing context including time-to-live for the offer (that has to match CR TTL)
        - purchase price
        - simulation royalties 
        - listing fee (if applicable)
        - other relevant information
    - Model instance and preview is uploaded to Cache Repository (CR) and will be available there until specified time-to-live amount of time passes
        - Cache Repository (CR) provides endpoint to query model instance (MI) in encrypted form: ex: GET https://host/api/tpro/model/{hash}
        - Cache Repository (CR) provides endpoint to query model instance (MI) preview in raw form, that contains part of the model instance (MI): ex: GET https://host/api/tpro/model/{hash}/preview 
    - Proof of Offer (PoO) is uploaded to Offer Repository (OR) and will be available there until specified time-to-live amount of time passes
    - (Optionally) TPRO marketplace (MAR) periodically publishes merkle tree root of active offers on-chain to indicate changes in offer list
- (Eventually) After TTL of the model instance (MI) publishing offer passes it is removed permanently from Cache Repository (CR) and Offer Repository (OR)
    - (Optionally) User (USR) can renew offer restarting process 3.1.3
    - (Optionally) Model Instance (MI) can be queried from Storage Providers

<a name="models-storing"></a>
### Storing Model Instance

Storing model instance is a process defined as follows:
- Storage provider (SP) queries information about active offers from Offer Repository (OR)
- Storage provider (SP) selects interesting offers and for each:
    - Storage provider (SP) download encrypted model instance (MI) and its preview from Cache Repository (CR) 
    - Storage provider (SP) provides endpoint to query model instance (MI) in encrypted form from its own storage: ex: GET https://host/api/tpro/model/{hash} 
    - Storage provider (SP) provides endpoint to query model instance (MI) preview in raw form, that contains part of the model instance (MI): ex: GET https://host/api/tpro/model/{hash}/preview 
    - Storage provider (SP) registers hash of newly stored model instance (MI) within Model Instance Registry (MIRC) contract available on TPRO network together with Proof of Offer (PoO).
        - Model Instance Registry (MIRC) registers model instance (MI) and its Proof of Offer (PoO)
        - Model Instance Registry (MIRC) contract emits event indicating registration of model instance (MI)
        - Event is publicly visible and can be used by User (USR) to notice new model instance and get its  routing information
    - (Optionally) TPRO Marketplace (TMAR) observes new storage registration, checks its authenticity and approves by triggering corresponding proofs (PoO) on Model Instance Registry (MIRC)

<a name="models-previewing"></a>
### Previewing Model Instance

Previewing model instance is a process defined as follows:
- User (USR) wants to preview a model instance (MI)
- User (USR) uses their wallet to query model instance routing information from Model Instance Registry (MIRC) 
- User (USR) selects preferred Storage Provider (SP)
    - User (USR) queries model instance preview from its storage using routing information received from Model Instance Registry (MIRC) - ex: https://host/api/tpro/model/{hash}/preview 

<a name="models-purchasing"></a>
### Purchasing Model Instance

Purchasing model instance is a process defined as follows:
- User (USR) preview model instance following steps defined in process 3.1.5
- User (USR) decides to purchase a model instance (MI)
- User (USR) uses their wallet to create Proof of Purchase, which is an atomic structure allowing making off-chain payment in exchange for model instance (MI)
    - Storage Provider (SP) triggers Proof of Purchase (PoP) on-chain using Proof Registry (PR)
        - Payment is automatically executed from User (USR) to Storage Provider (SP)
    - User (USR) queries model instance from its storage using routing information received from Model Instance Registry (MIRC) - ex: https://host/api/tpro/model/{hash}
    - User (USR) can decrypt queried model instance using TPRO App (TAPP) by providing the same Proof of Purchase (PoP)
        - (Optionally) TPRO App (TAPP) verifies Proof of Purchase

<a name="tokenomics"></a>
## Tokenomics

Tokenomics are structures used for modeling token distribution. Tokenomics need to be divided into two substructures: Tokenomics schemes (TS) and Tokenomics instances (TI). Schemes are structures that define the format of the tokenomics compatible with TPRO network, while Instances are schemes filled with actual data. 

Tokenomics schemes are administered only by the TPRO network itself in order to establish protocol rules and it may be possible for more than one scheme to exist at time. Tokenomics schemes are also not monetized as it is part of the protocol, meaning the access to it should be public and unencrypted. Since there is no monetization involved with schemes, the solution will require TPRO network to run a service that enables querying of the model schemes directly from TPRO network, not storage providers.

Tokenomics instances can be created by tokenomics providers, which are types of end-users. Tokenomics instances can be created in online and offline modes using TPRO application (or TPRO marketplace). 

In relation to tokenomics, there needs to exist a ways for:
- TPRO (T) to publish tokenomics scheme
- User (USR) to retrieve tokenomics scheme
- User (USR) to publish tokenomics instance
- Storage Provider (SP) to store tokenomics instance
- User (USR) to preview tokenomics instance

<a name="tokenomics-scheme-publishing"></a>
### Publishing Tokenomics Scheme

Publishing tokenomics scheme is a process defined as follows:
- TPRO (T) defines a new tokenomics scheme (TS)
- TPRO (T) activates a new tokenomics scheme (TS), computes its hash and publishes its contents in Ref. Data Repository (RDR)
    - Ref. Data Repository (RDR) publishes tokenomics scheme under unique API link, consisting of tokenomics scheme (TS) hash, which returns tokenomics scheme (TS) definition
- TPRO (T) registers hash of newly defined tokenomics scheme (TS) within Tokenomics Scheme Registry (TSRC) contract available on TPRO network
    - Tokenomics Scheme Registry (TSRC) contract emits event indicating registration of tokenomics scheme (TS)
    - Event is publicly visible and can be used by User (USR) to notice new tokenomics scheme and get its  routing information

<a name="tokenomics-scheme-retrieving"></a>
### Retrieving Tokenomics Scheme

Retrieving tokenomics scheme is a process defined as follows:
- User (USR) wants to retrieve a tokenomics scheme (TS)
- User (USR) uses their wallet to query tokenomics scheme routing information from Tokenomics Scheme Registry (TSRC) 
- User (USR) uses their wallet and receives routing information to query tokenomics scheme (TS) from Ref. Data Repository (RDR)
    - User (USR) receives tokenomics scheme (TS)

<a name="tokenomics-publishing"></a>
### Publishing Tokenomics Instance

Publishing tokenomics instance is a process defined as follows:
- User (USR) retrieves tokenomics scheme according to flow 3.2.2
- User (USR) uses TPRO app (TAPP) in online or offline mode to fill tokenomics scheme (TS) therefore creating a new tokenomics instance (TI)
    - User (USR) encrypts tokenomics instance (TI) and creates a preview using TPRO app (TAPP)
- User (USR) publishes tokenomics instance (MI) through TPRO marketplace (TMAR)
    - User (USR) uses their wallet to provide Proof of Publishing (PoP) to TPRO marketplace (TMAR) that contains:
        - information identifying created tokenomics instance (MI)
        - publishing context including time-to-live for the offer (that has to match CR TTL)
        - purchase price
        - simulation royalties 
        - listing fee (if applicable)
        - other relevant information
    - Tokenomics instance and preview is uploaded to Cache Repository (CR) and will be available there until specified time-to-live amount of time passes
        - Cache Repository (CR) provides endpoint to query tokenomics instance (TI) in encrypted form: ex: GET https://host/api/tpro/tokenomics/{hash}
        - Cache Repository (CR) provides endpoint to query tokenomics instance (TI) preview in raw form, that contains part of the tokenomics instance (TI): ex: GET https://host/api/tpro/tokenomics/{hash}/preview 
    - Proof of Offer (PoO) is uploaded to Offer Repository (OR) and will be available there until specified time-to-live amount of time passes
    - (Optionally) TPRO marketplace (TMAR) periodically publishes merkle tree root of active offers on-chain to indicate changes in offer list
- (Eventually) After TTL of the tokenomics instance (TI) publishing offer passes it is removed permanently from Cache Repository (CR) and Offer Repository (OR)
    - (Optionally) User (USR) can renew offer restarting process 3.2.3
    - (Optionally) Tokenomics Instance (TI) can be queried from Storage Providers

<a name="tokenomics-storing"></a>
### Storing Tokenomics Instance

Storing tokenomics instance is a process defined as follows:
- Storage provider (SP) queries information about active offers from Offer Repository (OR)
- Storage provider (SP) selects interesting offers and for each:
    - Storage provider (SP) download encrypted tokenomics instance (TI) and its preview from Cache Repository (CR) 
    - Storage provider (SP) provides endpoint to query tokenomics instance (TI) in encrypted form from its own storage: ex: GET https://host/api/tpro/tokenomics/{hash} 
    - Storage provider (SP) provides endpoint to query tokenomics instance (TI) preview in raw form, that contains part of the tokenomics instance (TI): ex: GET https://host/api/tpro/tokenomics/{hash}/preview 
    - Storage provider (SP) registers hash of newly stored tokenomics instance (TI) within Tokenomics Instance Registry (TIRC) contract available on TPRO network together with Proof of Offer (PoO).
        - Tokenomics Instance Registry (TIRC) registers tokenomics instance (TI) and its Proof of Offer (PoO)
        - Tokenomics Instance Registry (TIRC) contract emits event indicating registration of tokenomics instance (TI)
        - Event is publicly visible and can be used by User (USR) to notice new tokenomics instance and get its  routing information
- (Optionally) TPRO Marketplace (TMAR) observes new storage registration, checks its authenticity and approves by triggering corresponding proofs (PoO) on Tokenomics Instance Registry (TIRC)

<a name="tokenomics-previewing"></a>
### Previewing Tokenomics Instance

Previewing tokenomics instance is a process defined as follows:
- User (USR) wants to preview a tokenomics instance (TI)
- User (USR) uses their wallet to query tokenomics instance routing information from Tokenomics Instance Registry (TIRC) 
- User (USR) selects preferred Storage Provider (SP)
    - User (USR) queries tokenomics instance preview from its storage using routing information received from Tokenomics Instance Registry (TIRC) - ex: https://host/api/tpro/tokenomics /{hash}/preview 

<a name="simulations"></a>
## Simulations

Simulations are processes used by simulation operators to simulate the future behavior of token system, including token flows, allocations, performance, pricing and others. Simulations are by design performed by simulation operators that take input in the form of tokenomics instances, model instances and lifetime events. The output of simulations are the results that describe previously mentioned expectations.

Simulations can be performed by simulation operators, which are types of end-users. Simulation can be performed only in online modes using TPRO application (or TPRO marketplace). Simulations take data input from specific instances of data requested (chosen) by simulation operators, which needs to be paid far. 

Covering costs of simulations comes in two ways. First, when making a simulation the TPRO application (or TPRO marketplace) has to query requested data from storage providers, which needs to be paid from overall simulation fee. This is to ensure a constant revenue stream for modelers, tokenomics providers and Lifetime event providers, while reducing threats of potential piracy or repackaging (more on this in managing risks section). In contrast to simple purchases of instances, the purchases done through simulation are distributed by TPRO application (or TPRO marketplace) through Proof of Simulation. Proof of Simulation is intended to be a simple proof that wraps around multiple Proof of Offers and Proof of Purchases that needs to be distributed between multiple providers. The purpose for this proof is to enclose everything into one, atomic structure, ensuring that simulation either pays everyone, or it fails (if the fee specified by the simulation operator is not enough to cover all costs).

Aside from querying instances which are used for simulation, there is also the matter of processing resources, as simulation tends to be expensive in terms of processing power. Payment for processing power has yet another cost that is intended to be part of the fee, which similar to other fees will be enclosed within Proof of Simulation and paid to the processing provider. Since it is not possible to provide precise estimation on how much cost will be associated with the simulation before performing ones, the proposed fee model has to enable dynamic pricing and constraining. The proposed approach is to copy the solution blockchain platforms are using for processing contracts, meaning that the fee for processing power will consist of unit price which can be used for prioritization and limit on how many processing units can be spent in order to finish simulation. 
The maximum processing costs for simulation would then be equal to unitPrice*unitLimit. If simulation is performed before that limit is reached it finishes successfully. The chargeback for the unused fee should then be transferred back to the simulation operator. If simulation is not performed before the limit is reached, the TPRO application (or TPRO marketplace) should immediately drop its processing while providing appropriate error message and error code to the simulation operator. Storage fees for failed simulation should still be distributed to the storage providers, as they performed their service correctly.

To sum up, the cost of running a simulation should be covered by Proof of Simulation that will be provided by the simulation operator. Proof of Simulation should consist of, but not be limited to, choice of data instances to use and a fee for storage/processing. 
The overall cost of running simulation may be represented in following formula: cost = storagePriceForAllModels + processingUnitPrice*processingUnitLimit

Monetization of simulation, in contracts to data instances, would be provided in one way only. Purchasing simulation results directly from storage providers for local storage / other needs. Purchasing simulation results directly from storage providers would happen between consumer and storage provider through dedicated proof. 

Due to the monetized nature of simulation results, they need to be fully encrypted while stored within a storage provider, so storage provider does not have free access to the contents of the data (it is not able to workaround TPRO payments). Decryption of the data should be possible only by the original owner (simulation operator) or TPRO marketplace itself (while providing proof of purchase). Additionally, there needs to exist some kind of preview of simulation results, that would contain unecrypted part of the original simulation in order to enable consumers to view it and use that viewing as decision maker whether to make or not make purchase (similar to how parts of PDF books are provided for free to incentivize potential buyer).

In relation to simulations, there needs to exist a ways for:
- User (USR) to perform simulation
- User (USR) to publish simulation results
- Storage Provider (SP) to store simulation results
- User (USR) to preview simulation results
- User (USR) to purchase simulation results

<a name="simulations-performing"></a>
### Performing Simulation

Performing simulation is a process defined as follows:
- User (USR) uses TPRO app (TAPP) in online mode to initiate simulation
    - User (USR) configures simulation environment
        - User (USR) selects model instances to use
        - User (USR) selects tokenomics instance to use
        - (Optionally) User (USR) selects lifetime events to use
        - User (USR) provides additional input if necessary
        - User (USR) provides simulation settings
    - User (USR) configures simulation fees
        - User (USR) configures base fee (cumulative storage fee)
        - User (USR) configures processing unit price
        - User (USR) configures processing unit size limit
    - User (USR) configures other settings if applicable
    - User (USR) runs simulation
        - User (USR) uses their wallet to provide Proof of Simulation (PoS) to TPRO application (TAPP) that contains:
        - Selected model instances including their hashes and storage providers identifications
        - Selected tokenomics instances including their hashes and storage providers identifications
        - (Optionally) selected lifetime events including their hashes and storage providers identifications
        - (Optionally) other simulation input
        - Proof of Purchase for each model
        - Proof of Purchase for each tokenomics
        - Proof of Purchase for each lifetime events
        - Processing unit price
        - Processing unit size limit
        - Processing provider identification
        - Proof of Offer reference (not actual PoO, but identification which proof it relates to)
- TPRO application (TAPP) finishes simulation 
    - User (USR) receives results
    - Proof of Simulation is send to blockchain
- Simulation results and preview is uploaded to Cache Repository (CR) and will be available there until specified time-to-live amount of time passes
    - Cache Repository (CR) provides endpoint to query simulation results (SRI) in encrypted form: ex: GET https://host/api/tpro/simulations/{hash}
    - Cache Repository (CR) provides endpoint to query simulation results (SRI) preview in raw form, that contains part of the simulation results (SRI): ex: GET https://host/api/tpro/simulations/{hash}/preview 
    - Proof of Offer (PoO) is uploaded to Offer Repository (OR) and will be available there until specified time-to-live amount of time passes
    - (Optionally) TPRO marketplace (TMAR) periodically publishes merkle tree root of active offers on-chain to indicate changes in offer list
- (Eventually) After TTL of the simulation results (SRI) publishing offer passes it is removed permanently from Cache Repository (CR) and Offer Repository (OR)
    - (Optionally) User (USR) can renew offer restarting process
    - (Optionally) Simulation results (SRI) can be queried from Storage Providers

<a name="simulations-publishing"></a>
### Publishing Simulation Instance

Publishing simulations results instance is a process defined as follows:
- TPRO application (TAPP) finishes simulation 
    - User (USR) receives results
    - Proof of Simulation is send to blockchain
- Simulation results and preview is uploaded to Cache Repository (CR) and will be available there until specified time-to-live amount of time passes
    - Cache Repository (CR) provides endpoint to query simulation results (SRI) in encrypted form: ex: GET https://host/api/tpro/simulations/{hash}
    - Cache Repository (CR) provides endpoint to query simulation results (SRI) preview in raw form, that contains part of the simulation results (SRI): ex: GET https://host/api/tpro/simulations/{hash}/preview 
    - Proof of Offer (PoO) is uploaded to Offer Repository (OR) and will be available there until specified time-to-live amount of time passes
    - (Optionally) TPRO marketplace (TMAR) periodically publishes merkle tree root of active offers on-chain to indicate changes in offer list
- (Eventually) After TTL of the simulation results (SRI) publishing offer passes it is removed permanently from Cache Repository (CR) and Offer Repository (OR)
    - (Optionally) User (USR) can renew offer restarting process
    - (Optionally) Simulation results (SRI) can be queried from Storage Providers

<a name="simulations-storing"></a>
### Storing Simulation Instance

Storing simulation results instance is a process defined as follows:
- Storage provider (SP) queries information about active offers from Offer Repository (OR)
- Storage provider (SP) selects interesting offers and for each:
    - Storage provider (SP) download encrypted simulation results instance (SRI) and its preview from Cache Repository (CR) 
    - Storage provider (SP) provides endpoint to query simulation results instance (SRI) in encrypted form from its own storage: ex: GET https://host/api/tpro/simulation_results/{hash} 
    - Storage provider (SP) provides endpoint to query simulation results instance (SRI) preview in raw form, that contains part of the simulation results instance (SRI): ex: GET https://host/api/tpro/simulation_results/{hash}/preview 
    - Storage provider (SP) registers hash of newly stored simulation results instance (SRI) within Simulation Results Instance Registry (SRIRC) contract available on TPRO network together with Proof of Offer (PoO).
        - Simulation Results Instance Registry (SRIRC) registers simulation results instance (SRI) and its Proof of Offer (PoO)
        - Simulation Results Instance Registry (SRIRC) contract emits event indicating registration of simulation results instance (SRI)
        - Event is publicly visible and can be used by User (USR) to notice new simulation results instance and get its  routing information
    - (Optionally) TPRO Marketplace (TMAR) observes new storage registration, checks its authenticity and approves by triggering corresponding proofs (PoO) on Simulation Results Instance Registry (SRIRC)


<a name="simulations-previewing"></a>
### Previewing Simulation Instance

Previewing simulation results instance is a process defined as follows:
- User (USR) wants to preview a simulation results (SRI)
- User (USR) uses their wallet to query simulation results routing information from Simulation Results Instance Registry (SRIRC) 
- User (USR) selects preferred Storage Provider (SP)
    - User (USR) queries simulation results instance preview from its storage using routing information received from Simulation Results Instance Registry (SRIRC) - ex: https://host/api/tpro/simulation_results/{hash}/preview 

<a name="simulations-purchasing"></a>
### Purchasing Simulation Instance

Purchasing simulation results instance is a process defined as follows:
- User (USR) preview simulation results following steps defined in process 3.3.4
- User (USR) decides to purchase a simulation results instance (SRI)
- User (USR) uses their wallet to create Proof of Purchase, which is an atomic structure allowing making off-chain payment in exchange for simulation results instance (SRI)
    - Storage Provider (SP) triggers Proof of Purchase (PoP) on-chain using Payment Registry (PR)
        - Payment is automatically executed from User (USR) to Storage Provider (SP)
    - User (USR) queries simulation results instance from its storage using routing information received from Simulation Results Instance Registry (SRIRC) - ex: https://host/api/tpro/simulation_results/{hash}
    - User (USR) can decrypt queried simulation results instance using TPRO App (TAPP) by providing the same Proof of Purchase (PoP)
        - (Optionally) TPRO App (TAPP) verifies Proof of Purchase


<a name="analysis-reports"></a>
## Analysis Reports

Analysis reports are structures used for providing extra interpretation on top of simulation results. Analysis reports are performed by analysts that can query simulation results contents using TPRO application (or TPRO marketplace) and then use that information to create additional insights and reviews which can be consumed then by other users (similar to TradingView ideas).

Analysis reports can be created by analysts, which are types of end-users. Analysis reports can be created in online and offline modes using TPRO application (or TPRO marketplace). Analysis reports contain specific data provided by the analysts and they need to be monetized. Monetization of analysis reports comes in one way. Purchasing analysis reports directly from storage providers for local storage / other needs. Purchasing analysis reports directly from storage providers would happen between consumer and storage provider through dedicated proof. 

Due to the monetized nature of analysis reports, they need to be fully encrypted while stored within a storage provider, so a storage provider does not have free access to the contents of the data (it is not able to workaround TPRO payments). Decryption of the data should be possible only by the original owner (analyst) or TPRO marketplace itself (while providing proof of purchase). Additionally, there needs to exist some kind of preview of analysis reports, that would contain unencrypted part of the original report in order to enable consumers to view it and use that viewing as decision maker whether to make or not make purchase (similar to how parts of PDF books are provided for free to incentivize potential buyer).

In relation to tokenomics, there needs to exist a ways for:
- User (USR) to publish analysis reports
- Storage Provider (SP) to store analysis reports
- User (USR) to preview analysis reports
- User (USR) to purchase analysis reports

<a name="lifetime-events"></a>
## Lifetime Events

Lifetime events are structures used to model information about the past of a particular blockchain. Lifetime events purpose is to provide historical information about active projects which can be used to enhance simulation quality and improve results.

Lifetime events can be created by lifetime providers, which are types of end-users. Lifetime events can be provided online using the TPRO application (or TPRO marketplace). Lifetime events instances contain actual data about external networks provided by the lifetime provider.

<a name="rewarding"></a>
## Rewarding

In order to provide incentivization to use TPRO tokens throughout the network, the rewarding of different participants have to be represented in TPRO tokens. This section focuses on different revenue streams for each actor, to help the readers to better understand economic incentives.

<a name="rewarding-model-providers"></a>
### Rewarding Model Providers

Model providers provide TPRO networks with model instances which can be used for simulations and analysis. In order to define model instances, model providers require model schemes and access to TPRO applications, both of which are available to them for free.

Model providers pay for:
- listing fees which are used to cover on-chain interactions for TPRO marketplace and storage providers

Model providers are being paid for:
- Model instances purchases
- Model instances uses in simulations

<a name="rewarding-tokenomics-providers"></a>
### Rewarding Tokenomics Providers

Tokenomics providers provide TPRO networks with tokenomics instances which can be used for simulations and analysis. In order to define tokenomics instances, tokenomics providers require tokenomics schemes and access to TPRO applications, both of which are available to them for free.

Tokenomics providers pay for:
- listing fees which are used to cover on-chain interactions for TPRO marketplace and storage providers


Tokenomics providers are being paid for:
- Tokenomics instances purchases
- Tokenomics instances uses in simulations

<a name="rewarding-simulation-operators"></a>
### Rewarding Simulation Operators

Simulation operators provide the TPRO network with results of simulations that they schedule. In order to perform simulation simulation, the operator needs to select the models, tokenomics and lifetime events to be used within simulation which, depending on selection, may be paid or free, but most likely paid. In order to perform simulation, the operator needs access to TPRO applications, which are available to them for free.

Simulation operators pay for:
- Model instances purchases which covers modeler that created model and storage provider that enabled access to it
- Tokenomics instances purchases which covers tokenomics provider that created tokenomics and storage provider that enabled access to it
- Lifetime events purchases which covers Lifetime providers that provided events and storage provider that enabled access to it
- Processing power provided by processing provider

Simulation operators are paid for:
- Simulation results purchases

<a name="rewarding-analysts"></a>
### Rewarding Analysts

Analysts provide the TPRO network with analysis, which are interpretations of simulation results provided by simulation operators. In order to perform analysis, analysts have to purchase simulation results which are paid. Analysts also require TPRO applications, which are available to them for free.

Analysts pay for:
- Simulation results which covers simulation operator that created simulation and storage provider that enables access to it

Analysts are paid for:
- Analysis results purchases

<a name="rewarding-storage-providers"></a>
### Rewarding Storage Providers

Storage providers provide the TPRO network with storage space to store models, tokenomics, Lifetime events, simulation results and analysis results. Additionally the provide replication when multiple providers store the same data. 

Storage providers pay for:
- None

Store providers are paid for:
- Model instances purchases
- Model instances uses in simulations
- Tokenomics instances purchases
- Tokenomics instances uses in simulations
- Simulation results purchases
- Analysis results purchases

<a name="rewarding-processing-providers"></a>
### Rewarding Processing Providers

Processing providers provide the TPRO network with processing power to perform (compute) simulations.

Processing providers pay for:
- None

Processing providers are paid for:
- Simulations computations

<a name="rewarding-tpro"></a>
### Rewarding TPRO

TPRO provides applications and tools required for making models, tokenomics, aggregating Lifetime events and performing simulations and analysis. As TPRO protocol provider, TPRO is rewarded for each purchase or computation (a marginal fee on top of what providers make)

TPRO pays for:
- Application and tools development, maintenance and hosting that are not part of TPRO network blockchain monetization infrastructure

TPRO is paid for:
- Marginal fee on top of each purchase and computation


<a name="further-incentivization"></a>
### Further Incentivization

In order to further incentivize network participants and strengthen the overall adoption of the network, TPRO may consider airdrops of tokens and/or competitions , both of which may divide amount of rewarded tokens based on participant activity on the network (judged by on-chain metrics available within TPRO network).

<a name="proofs"></a>
## Proofs

In order to properly implement the TPRO network, it will be required to implement a few types of proofs. Those proofs will be used in order to ensure monetization of the platform, efficient exchange of tokens in exchange for data, proof network metrics related to distribution and quality and, last but not least, incentive distribution of the network. All of the proofs will be provided within payment channels established between participants of the TPRO network.

<a name="proofs-poo"></a>
### Proof of Offer (PoO)

Proof of Offer is a special type of proof exchanged between users that created any data instance that they wish to store on the TPRO network, including models, tokenomics, events, simulations, analysis reports etc. Proof of Offer main purpose is to broadcast to storage providers information that a new data instance has been produced that needs storage space, and provide them information on royalties that data instance author wishes to receive for use of the data.

Proof of Offer includes following information:
- information identifying created data instance
- publishing context including time-to-live for the offer (that has to match CR TTL)
- purchase price
- simulation royalties 
- listing fee (if applicable)
- other relevant information

Proof of Offer may carry payment information alongside it, but it is not intended for the data author to pay storage providers for picking up its data, as it may lead to malicious behavior including draining of data funds, false storage, false providers etc. The payment that may be carried within Proof of Offer is only meant to: a) pay TPRO listing fees b) cover storage providers costs associated with generation of Proof of 
Storage

<a name="proofs-pos"></a>
### Proof of Storage (PoS)

Proof of Storage is lightweight proof that is provided by a storage provider and settled on the TPRO network as soon as that provider modifies its storage. Proof of Storage is meant to broadcast information to TPRO participants about routing of data instances, meaning publishing the consistent information which data is being stored where, including replication information.

Proof of Storage includes following information:
- Information identifying stored data data instance
- Reference to storage information

Proof of Storage does not carry payment information alongside it. Costs of producing proof of storage is covered mostly by delegating context information to Proof of Offer that has been paid by the data author. 

<a name="proofs-pop"></a>
### Proof of Purchase (PoP)

Proof of Purchase is a proof that indicates purchase of a particular data instance from a storage provider. Proof of Purchase is the main revenue for storage providers and its is generated in two instances. First one, while the user wants to purchase information directly from the storage provider (for the purpose of using it off-chain) or while the user wants to use information while performing simulation (for the purpose of using data as simulation input). 

Proof of Purchase includes following information:
- Payer
- Payee
- Amount paid
- Information identifying created data instance
- Reference to storage information
- other relevant information

<a name="proofs-posim"></a>
### Proof of Simulation (PoSIM)

Proof of Simulation is a proof that is generated when performing simulation. The main purpose for the proof is to ensure atomicity of operations that are performed underneath the simulation suite hood. In other words Proof of Simulation wraps all other  proofs which may be required for purchasing storage and/or processing power required for simulation, and enables execution of those payments all at once.
