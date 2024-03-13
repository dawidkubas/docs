# Objectives and Design Considerations

- [Design Considerations](#design-considerations)
    - [Delegation of Processing Power](#delegation-of-processing-power)
    - [Delegation of storage](#delegation-of-storage)
    - [Data providers vs resource providers domain gap](#data-providers-vs-resource-providers)
    - [TPRO at the center](#tpro-at-the-center)
    - [File sizes](#file-sizes)
    - [Replication](#replication)
    - [On-chain reporting](#on-chain-reporting)
- [Design Overview](#design-overviewdesign-overview)
    - [TPRO network overview](#tpro-network-overview)
- [Design Objectives](#design-objectives)

<a name="introduction"></a>
## Introduction

The objectives and design considerations serve as the foundational pillars guiding the development and evolution of the TPRO Network. This section tries to stay as high-level  focused as possible, so it will not go into details on how to achieve mentioned characteristics and/or what kind of tech use, but rather how it should be constructed and how the solution should behave. This section is further divided into sub-sections, each one dedicated to each topic.

<a name="design-considerations"></a>
## Design Considerations

Design considerations serve as the guiding principles for every aspect of the project’s architecture, ensuring that each component contributes to the overall objectives and goals. By addressing key design considerations, TPRO Network aims to create a robust infrastructure that maximizes the utility of its native token while offering a seamless and secure environment for users to engage with the platform.

<a name="delegation-of-processing-power"></a>
### Delegation of Processing Power

One crucial design consideration for the project is the strategic shift towards delegating processing power, particularly computation resources, from centralized providers to decentralized ones. By decentralizing the distribution of operational costs associated with running the network, the burden is spread across multiple providers instead of relying solely on a single entity. This approach not only promotes a more equitable distribution of resources but also incentivizes providers to offer their services directly to end-users in exchange for tokens, thereby fostering a sustainable and self-sustaining ecosystem within the network.

<a name="delegation-of-storage"></a>
### Delegation of Storage

Another important design consideration for the project involves the delegation of storage space from centralized providers to decentralized ones, with the aim of spreading operational costs across multiple providers rather than relying solely on a single entity. In addition to this decentralization, ensuring data replication is crucial to the project's success. Replicating data across multiple providers simultaneously extends the artifact's lifetime and enhances network resilience by enabling fallback options. This approach not only distributes operational costs more evenly but also enhances data durability and availability, thereby bolstering the reliability and sustainability of the network as a whole.

<a name="data-providers-vs-resource-providers"></a>
### Data providers vs resource providers domain gap

The project actors can be categorized into two distinct groups: those who provide models for computation/storage (consumer-type) and those who offer resources for computation/storage (provider-type). However, this division creates a domain gap between them, as the consumer-type actors lack a space to temporarily store their data before presenting it to the provider-type actors for replication. Essentially, there is a missing mediation layer between these two groups. This mediation layer must be introduced to facilitate seamless interaction between actors, ensuring that consumer-type actors can easily present their data to provider-type actors without additional costs or complexity. Importantly, this mediation layer should not replace computation/storage providers but rather act as a facilitator to streamline the exchange process and enhance overall network efficiency.

<a name="tpro-at-the-center"></a>
### TPRO at the center

While the TPRO network aspires to be decentralized, it must evolve in a manner that ensures the longevity and relevance of the TPRO entity without rendering it obsolete. It is essential to strike a balance where decentralization enhances the network's resilience and transparency while maintaining the core value proposition of TPRO. Additionally, careful consideration must be given to prevent creating new leverage for competitors to replicate the TPRO network from scratch and potentially replace it. This necessitates ongoing innovation and adaptation to emerging technologies and market dynamics, ensuring that TPRO remains a leader in the field while embracing decentralization as a foundational principle.

<a name="file-sizes"></a>
### File sizes

A critical aspect of the platform as a whole is the size of the data being exchanged between its components, which can reach gigabytes in scale. This substantial file size imposes significant restrictions on how such data can be stored and shared efficiently. During the development of the system, it is paramount to keep this constraint in mind and ensure that proposed solutions are viable for handling files of such sizes. By addressing this challenge effectively, the platform can facilitate seamless data exchange and processing, unlocking its full potential for users while maintaining optimal performance and scalability.

<a name="replication"></a>
### Replication

The replication of data within storage providers in the project is of major importance to ensure the uptime and reliability of the system. It is not sufficient merely to store artifacts produced by the TPRO network within a single service provider; rather, data replication is crucial to safeguard against potential disruptions. By replicating data across multiple storage providers, the system can mitigate the risk of downtime in the event that one or more providers become unavailable for any reason, such as maintenance, technical issues, or even natural disasters. This redundancy ensures continuous access to critical data and services, maintaining the uptime of the system and enhancing its resilience in the face of unforeseen challenges.

<a name="on-chain reporting"></a>
### On-chain reporting

TPRO Network stands as a project deeply committed to incentivizing the utility of its native token, emphasizing the need for proposed solutions to reflect this imperative. As such, delegating some components directly to the blockchain (on-chain) becomes a strategic necessity. These blockchain-based components must prioritize transparency and be payable using the TPRO token, aligning with the network's overarching vision. However, it is equally crucial to ensure a sensible division between on-chain and off-chain elements. This approach ensures that the incorporation of on-chain elements does not hinder the performance or scalability of the network and does not impose artificial costs on consumers or providers. By striking this balance, TPRO Network can optimize its operations while enhancing the value proposition of its native token.

<a name="design-overview"></a>
## Design Overview

The design overview of the TPRO Network provides a comprehensive overview of the platform's architecture, highlighting key components and their interconnections in relation to business requirements. This overview serves as a roadmap for understanding how a system is supposed to be shaped.

<a name="tpro-network-overview"></a>
### TPRO Network Overview

TPRO network offers following products:

- Modeling Suite which enables definitions of models and tokenomics
- Simulation Suite (“Simulate”) which provides the execution of economics (tokenomics) on top of multiple demand, supply, and market scenarios (models)
- Analysis Suite (“Analyze”) which enables extending simulation results with human-made interpretation of the results by analysts
- Insights Exchange (“Exchange”) which enables marketplace for existing models, tokenomics, simulations and analysis

TPRO network offers following services:

- Portal (which consists of multiple TPRO products)
- Fullnode which is implementation of a single node; multiple of which are required for running the network
- Off-chain Worker which is implementation of a bridge between on-chain and off-chain data and uses throughout multiple services to allow them to serve its purpose
- Wallet which is end-user application which can be used for interacting with TPRO network, holding tokens, making payments, calling offchain services, signing and authorizing services etc
- SDK which is implementation of TPRO protocol and enables instantiation and transporting of each one of TPRO network structures and primitives
- Contract Spec which is specification of contracts running on TPRO network
- Protocol Spec which is specification of protocol used between TPRO network products and services

TPRO network is built around following architectural characteristics:

- Account-based L1 settlement system (EVM)
- Oracle-based L2 supplementary layer (EVM)
- Blockchain as knowledge repository for deployment
- Blockchain as token distribution repository
- Low-finalization transfers with high throughput
- Low-transaction cost and eco friendly algorithms
- Programmability through EVM contracts
- Delegated proof of stake consensus
- Tiered architecture based on Aurora
- DAO Governance
- Highly configurable & easy for maintenance

TPRO network consists of five types of entities which are used to represent data. Those are models, tokenomics, analysis, simulations and Lifetime events. 

- Models are used to represent supply and demand of different tokens and the contextual state of the world (bull market, bear market, accumulation, distribution etc). 
- Tokenomics are used to represent the distribution and allocation of tokens. 
- Lifetime events are used as information about past events that impacts the model or tokenomics such as transfers between users or unlocks that already happened (which is required in case of modeling tokenomics of existing platform that is already available and is being actively used by their users)
- Simulations are used to model information about the future of tokenomics, which is useful in order to predict impact of different models on the overall state of the ecosystem, including price prediction. Simulations rely on three inputs - models, tokenomics and (optionally) Lifetime events.
- Analysis are used to interpret simulation results. They represent additional human-input on top of machine-based simulation.

TPRO network provides multiple tools to define, create, use and interpret data. Those tools are used by different type of users including:

- Consumers which are type of users that can read (consume) data
- Modelers which are a type of users that can use schemes for models, fill them with contents and create model instances
- Tokenomics Provider which are a type of users that can use schemes for tokenomics, fill them with contents and create tokenomics instances
- Simulation Operators which are a type of users which can take existing models, tokenomics and/or lifetime events and run simulations in order to receive the results on how given system should behave
- Analysts which are type of users that can the results of performed simulations and add their own understanding/interpretation of the results
- Lifetime Providers are type of users that can track major events on existing platforms, filter ones which are important in case of tokenomics and provide them for the purpose of simulating on real-data
- Storage Providers are type of users which provide storage for storing model instances, tokenomics instances, lifetime events, simulation results and analysis results
- Processing Providers are type of users which provide processing/computation power for performing simulations
- TPRO is special type of use that represents TPRO as an entity within TPRO network architecture
- Validators are type of users which are running validator nodes
- Maintainers are type of users which are running non-validating nodes (inc. lite nodes and archive nodes)
- Oracle Operators are type of users which are running any type of oracles, including bridges, off-chain services, ingest controllers or any other peripheral services required for proper functioning of other services

<a name="design-objectives"></a>
## Design Objectives

TPRO network objectives are as follows:
- Decentralize the network further, increasing efficiency, resiliency and accessibility of the system 
- Delegate processing and storage from TPRO entity to willing providers; reduce operational costs for TPRO while enabling providers to receives rewards for the services they are providing
    - Delegate model instances storage to storage providers
    - Delegate tokenomics instances storage to storage providers
    - Delegate lifetime events storage to storage providers
    - Delegate simulation results storage to storage providers
    - Delegate analysis results storage to storage providers
    - Delegate simulation computation to processing providers
- Standardize TPRO protocol and ensure its a go-to solution for providing trust in tokenomics
- Incentivize TPRO token usage, therefore increasing its demand in the market
    - Enable “pay per data” model for models
    - Enable “pay per data” model for tokenomics
    - Enable “pay per data” model for lifetime events
    - Enable “pay per data” model for simulations
    - Enable “pay per data” model for analysis
