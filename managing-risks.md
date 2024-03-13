# Managing Risks

- [Lack of adoption](#lack-of-adoption)
- [Unfair rewarding](#unfair-rewarding)
- [Unfair randomness](#unfair-randomness)
- [Lack of permament storage](#lack-of-permament-storage)
- [Repackaging instances](#repackaging-instances)
- [Pirating instances](#pirating-instances)
- [Defective uptime](#defective-uptime)

<a name="introduction"></a>
## Introduction

Managing risks stands as a mandatory pillar for ensuring the successful delivery of complex systems and solutions. As project’s design and architecture evolves rapidly and uncertainties emerge, the need to proactively identify, analyze, and mitigate potential threats is crucial. This section of the documentation is dedicated to outlining our comprehensive approach to risk management, highlighting key strategies, methodologies, and tools employed to safeguard our project's objectives and deliverables from different threats. By effectively managing risks, we not only enhance the likelihood of project success but also bolster its resilience.

<a name="lack-of-adoption"></a>
## Lack of Adoption

The lack of adoption risk refers to the potential challenge of achieving widespread acceptance and usage of the technology within the targeted user base. Despite the numerous benefits and innovations that TPRO technology offers, such as transparency, security, and decentralization, the network will suffer from the lack of adoption at early stages. In order to mitigate that risk TPRO will be required to provide its own storage providers and processing power providers at start, before independent ones join the network. Mitigation of this risk does not create any requirement on the technology side of the project, but it needs to be understood that at start TPRO will still have to cover its own computation/storage, and slowly reduce the amount of proprietary providers as the new, independent ones join the network. Eventually all storing/processing provision will be possible to be delegated outside TPRO, but not at start.

<a name="unfair-rewarding"></a>
## Unfair Rewarding

Due to the extensive pool of providers offering access to specific data and/or computational resources, coupled with the dynamic nature of the system, evenly distributing rewards becomes challenging. The sheer scale of addresses within the network presents an obstacle to efficiently distributing rewards. In order to deal with this challenge the solution proposed random selection of providers within each access. However, while theoretically this would ensure even distribution of the rewards, in practice the reward distribution may be skewed especially in early and late phases of the network.

In the early phase the potentially even reward distribution may be skewed purely to low size of providers sample, meaning some of the providers may look as if they are being favored by TPRO leading to community  discontent. On the other hand, in leat phase because of the large amount of providers enabled within the network, there may be too low chance for being picked at all, leading to lack of continuity in reward scheme in which providers are either losing money (not being selected at all) or winning a jackpot (if there are selected). Similar situation exists in Bitcoin mining, however it is solved by merging into pools. Because of technical limitations such a solution would not be possible for TPRO network (at least not currently). Instead a slightly modified VRF function is proposed which would track already selected providers and favor the participants that were not selected yet ensuring even distribution, regardless of sample size.

<a name="unfair-randomness"></a>
## Unfair Randomness

The issue of unfair randomness due to the random nature of the selection algorithm can be effectively addressed by implementing a verifiable random function (VRF). VRF is a cryptographic function that generates a random output while also providing a proof that the output was generated correctly. By incorporating VRF into the selection algorithm, the TPRO network ensures an even distribution of providers picked for data access or computation, thereby eliminating the potential for unfair randomness.

<a name="lack-of-permament-storage"></a>
## Lack of Permament Storage

Delegation of storage to multiple providers may increase the system uptime and reliability, but it is not able to ensure permanent storage of the data within it. Even though multiple providers may replicate the same set of data, there is never guarantee that data will prevail long term, especially considering diminishing interest in older data, which may be outdated. In such scenarios the providers are incentivized to remove information that nobody wants, which by definition strikes out this solution as permanent storage. TPRO network may need some kind of archive to deal with this problem, but in the current iteration of the system it is unclear how to deal with this problem yet. What may be considered is a new type of provider called archive which would be paid by modelers directly for storing data outside the network, not for providing it within the network as current storage providers are meant to do. 

<a name="repackaging-instances"></a>
## Repackaging Instances

Due to the monetized nature of the data available within the TPRO network, there is a threat in which consumers would purchase specified data, then repackage it under different name and/or ID and republish on the network hoping it would be able to steal purchases from the original owner. This threat can be prevented by deterministic generation of hashes of each data packet, meaning that the same information being used twice would generate the same hash. That would disqualify the same information being repackaged as it would raise double-spend error when trying to be published a second time by another owner.

<a name="pirtaing-instances"></a>
## Pirating Instances

Due to the monetized nature of the data available within the TPRO network, there is a threat of piracy of the data. Consumers may purchase information from the network then publish it for free outside the network and advertise it to network users in order to direct the traffic from the network to their own repositories. This issue can be prevented by two security measures. The first security measure is deterministic hash generation mentioned in 4.5 Repackaging Instance, meaning the pirated information cannot be injected into the TPRO network from outside the second time. The second security measure is that the system would need to provide options for monetizing data while being used in simulation each time, rather than focus on purchases. Since pirated data would not be able to be put into the system a second time, and simulation would require use of existing data on the network to be paid for, there is no method of using the pirated information within simulation, majorly limiting its use. Additionally, because simulation tools are proprietary and not public in terms of source code, the pirating parties would not be able to replicate those tools out of the system.

<a name="defective-uptime"></a>
## Defective Uptime

Due to lack of possibility to granularly check the uptime of storage providers, they cannot be rewarded for storing the data, but rather for providing the access to that data. However, while this model reduces economical risk of paying for malfunctioning providers, it cannot eliminate such providers from being part of the network. There is a possibility to monitor the uptime of the providers due to the huge size of data they will be providing within each resource. The only way to deal with this threat is to penalize the providers which did not provide resource access when requested, when chosen by VRF function. This would reduce the amount of malfunctioning providers, theoretically enabling the solution to remove malfunctioning ones from being registered on the network. Unfortunately, this solution would not limit such providers to zero. It is currently unclear how to completely deal with the problem.
