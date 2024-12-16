---
title: "A Deep Dive into Machine Learning Model Security"
---


# A Deep Dive into Machine Learning Model Security

In today's rapidly evolving technological landscape, machine learning (ML) has emerged as a cornerstone of innovation. From powering autonomous vehicles to enabling smart home devices, ML models are embedded in countless applications, enhancing user experiences and optimizing system performance. However, as ML models become more integral to our daily lives, ensuring their security has become paramount. One of the most pressing concerns in this realm is **machine learning model security**, particularly the threats posed by model extraction attacks and the defenses designed to thwart them.

## The Rise of On-Device Machine Learning

Traditionally, ML models have been deployed on centralized servers, with devices acting as mere data collectors. However, the advent of powerful AI chips—such as Apple's Bionic Neural Engine, NVIDIA's Jetson series, Google's Coral Dev Board, and Qualcomm's AI-optimized SoCs—has revolutionized this paradigm. Modern smartphones, smart speakers, autonomous vehicles, and wearable gadgets now boast on-device ML capabilities, allowing for real-time processing without the latency and privacy concerns associated with cloud-based solutions.

On-device ML offers several advantages:

1.  **Privacy Preservation**: By processing data locally, sensitive user information doesn't need to traverse the internet, reducing the risk of data breaches.
2.  **Reduced Latency**: Real-time applications, such as intrusion detection systems or autonomous driving, benefit from immediate ML inference without the delays inherent in cloud communication.
3.  **Operational Efficiency**: On-device processing can lead to optimized system performance and intelligent decision-making at the edge.

However, these benefits come with a new set of security challenges. As ML models are deployed directly onto user devices, they become susceptible to a range of attacks aimed at extracting these models, potentially leading to intellectual property theft, privacy violations, and compromised system integrity.

## Understanding Machine Learning Model Extraction

**Model extraction**—also known as model stealing—is a form of attack where adversaries aim to recreate a target ML model by interacting with it. The extracted model can then be used for malicious purposes, such as crafting adversarial examples, performing membership inference attacks, or even deploying the model in unethical business competition.

### Why is Model Extraction a Concern?

1.  **Intellectual Property Theft**: ML models represent significant investments in research and development. Extracting these models can erode competitive advantages and lead to financial losses.
2.  **Privacy Violations**: Extracted models can be used to infer sensitive training data, compromising user privacy.
3.  **Security Risks**: In safety-critical applications like autonomous vehicles or medical diagnostics, model extraction can enable bypassing of safety checks or manipulation of system behaviors.

The critical question, then, is: **How can we protect ML models deployed on devices from being extracted and misused?**

## Threat Models: Decoding the Attack and Defense Perspectives

To effectively address ML model security, it's essential to understand the various **threat models**—the different ways in which adversaries might attempt to extract models and the corresponding defenses that can be employed. The recent research categorizes these threats into four distinct types from both the attacker's and defender's perspectives:

### 1. App-Based Attacks and Defenses

**Attackers' Perspective**:

-   **App-Based Attacks** involve gaining access to the application's files, often through public app marketplaces or exploiting vulnerabilities within IoT devices. Tools like APKTool, Jadx, and IDA Pro are commonly used to decompile or depackage apps, extracting ML models that may be stored in plaintext or obfuscated/encrypted formats.
-   **Success Rates**: Studies like DL Sniffer and ModelXray have revealed that a significant percentage of ML models in mobile apps are inadequately protected, with success rates for extraction attacks ranging from 34% to 76%.

**Defenders' Perspective**:

-   **App-Based Defenses** focus on obscuring ML models within app packages to make extraction difficult. Techniques include:
    -   **Encryption**: Utilizing Advanced Encryption Standard (AES) to encrypt model files before distribution.
    -   **Obfuscation**: Transforming model files into complex formats or converting them into code representations to thwart reverse engineering.
    -   **Customized Protection**: Implementing tools like Samsung’s Knox or Apple's CoreML, which provide encryption and obfuscation tailored to specific ML frameworks.

**Challenges**:

-   **Limited Adoption**: Defense solutions are often framework-specific or tied to vendor products, limiting their applicability across diverse apps and devices.
-   **Performance Overheads**: Encryption and obfuscation can introduce delays in app performance, impacting user experience.

### 2. Device-Based Attacks and Defenses

**Attackers' Perspective**:

-   **Device-Based Attacks** target the device's memory, attempting to extract models directly from RAM. Tools like Frida and GDB are employed to monitor memory regions where models are loaded during inference.
-   **Challenges**: These attacks are semi-automatic and require detailed knowledge of ML frameworks and memory architectures, making them less scalable and more dependent on specific conditions.

**Defenders' Perspective**:

-   **Device-Based Defenses** aim to protect models from being accessed in memory. Strategies include:
    -   **Trusted Execution Environments (TEEs)**: Running ML models within isolated environments like ARM TrustZone to prevent unauthorized memory access.
    -   **Secure Hardware**: Utilizing hardware-based protections like Secure Elements (SE) and Physically Unclonable Functions (PUFs) to safeguard model data.

**Challenges**:

-   **Resource Constraints**: TEEs can be limited in memory and computational capacity, restricting their ability to handle large models.
-   **Integration Complexity**: Implementing TEEs requires significant modifications to hardware and software, hindering widespread adoption.

### 3. Communication-Based Attacks and Defenses

**Attackers' Perspective**:

-   **Communication-Based Attacks** exploit side-channel information—such as power consumption, electromagnetic (EM) radiation, and cache access patterns—to infer model details.
-   **Techniques**: Methods like Flush+Reload and Rowhammer attacks have been used to extract layer structures and model weights with high accuracy.

**Defenders' Perspective**:

-   **Communication-Based Defenses** focus on mitigating side-channel leaks by:
    -   **Pattern Randomization**: Adding noise or randomizing memory access patterns to obscure model data.
    -   **Data Transformation**: Encrypting or transforming data transfers between secure and normal worlds to prevent information leakage.
    -   **Secure Architectures**: Designing hardware and software architectures that minimize side-channel vulnerabilities.

**Challenges**:

-   **Ad-Hoc Nature**: Side-channel attacks are highly specialized, making it difficult to develop universal defenses.
-   **Hardware Dependencies**: Effective defenses often require low-level hardware modifications, limiting their deployment across various devices.

### 4. Model-Based Attacks and Defenses

**Attackers' Perspective**:

-   **Model-Based Attacks** involve interacting with the ML model through its API, sending crafted inputs, and analyzing the outputs to reconstruct the model. Techniques like gradient-based methods and reinforcement learning are employed to optimize query efficiency and model fidelity.
-   **Examples**: Knockoff Nets and ML-Stealer have demonstrated high accuracy in replicating victim models with minimal query budgets.

**Defenders' Perspective**:

-   **Model-Based Defenses** aim to disrupt the model extraction process by:
    -   **Prediction Information Minimization**: Limiting the information returned by the model, such as only providing class labels instead of probability distributions.
    -   **Adaptive Misinformation**: Introducing inaccuracies or perturbations in the model's outputs to mislead attackers.
    -   **Differential Privacy**: Adding noise to the model's outputs or training process to obscure sensitive parameters.

**Challenges**:

-   **Balancing Accuracy and Security**: Introducing perturbations can degrade the model's performance for legitimate users.
-   **Adaptive Attackers**: Sophisticated attackers may find ways to bypass defenses by crafting queries that mimic natural user behavior.

## Evaluating the Effectiveness of Attacks and Defenses

The research highlights a significant gap between theoretical advancements and practical implementations in ML model security. While numerous attacks and defenses have been proposed, their real-world applicability varies considerably.

### Reproducibility Issues

A substantial number of research projects in model extraction security are either not open source or cannot be reproduced due to incomplete code, compatibility issues, or lack of documentation. This hampers the ability of practitioners to implement and test these solutions in real-world scenarios.

### Practical Effectiveness

-   **App-Based Attacks**: Tools like ModelXray have demonstrated high success rates in extracting models from a significant percentage of mobile apps. Despite the availability of defenses, many models remain unprotected, indicating a persistent vulnerability in the ecosystem.
-   **Device-Based Attacks**: While effective in controlled environments, device-based attacks like ModelXtractor face challenges in scalability and applicability across diverse device architectures and ML frameworks.
-   **Communication-Based Attacks**: Techniques such as DeepSniffer and DeepSteal can achieve high fidelity in model extraction but are limited by their dependence on specific side-channel information and hardware configurations.
-   **Model-Based Attacks**: Tools like ML-Doctor have shown that model-based extraction can be highly effective, but their success is contingent on the complexity of the target model and the attacker's ability to generate relevant queries.

### Computation Complexity and Power Consumption

The computational demands of both attacks and defenses play a crucial role in their practicality. For instance, encryption techniques like AES are efficient and introduce minimal overhead, making them suitable for on-device protection. In contrast, defenses like ShadowNet, which rely on TEEs, can impose significant runtime inference overheads, reducing their feasibility for widespread adoption.

Similarly, model extraction attacks can be resource-intensive, with tools like DeepSniffer and ML-Doctor consuming substantial power during operation. This not only impacts the device's performance but also increases the likelihood of detection by defenders monitoring abnormal resource usage patterns.

## Future Research Directions

Addressing the multifaceted challenges in ML model security requires a concerted effort to bridge the gap between research and practice. The following areas present promising avenues for future exploration:

### 1. Multi-User Sharing on-Device ML

Many IoT applications involve multi-user environments, such as smart homes or healthcare devices shared among multiple individuals. Protecting models in these settings necessitates robust defenses that can handle diverse user interactions without compromising model security or user privacy.

### 2. Federated Learning Integration

Federated Learning (FL) enables model training across multiple devices without centralizing data, enhancing privacy. However, FL systems are still vulnerable to model extraction attacks. Integrating techniques like Multi-party Computation (MPC) with FL can bolster defenses, ensuring that models remain secure even as they are collaboratively trained and updated.

### 3. Early Exit Models

Early exit architectures allow ML models to make quick predictions by exiting the network early for simple inputs. While beneficial for performance, these models can be exploited for model extraction by analyzing the exit points and inference times. Developing defenses that obscure these patterns without degrading model performance is crucial.

### 4. Cryptography-Based Defenses

Advanced cryptographic techniques, such as Homomorphic Encryption (HE) and Secure Multi-Party Computation (MPC), offer strong theoretical guarantees for model security. Future research should focus on optimizing these methods to reduce computational overhead, making them viable for real-world on-device applications.

### 5. Securing On-Device GPUs

As GPUs become more prevalent in on-device ML applications, securing them against model extraction attacks is essential. Solutions like GPUShield and D-Box, which provide memory protection and compartmentalization, offer promising directions for safeguarding models running on GPU accelerators.

### References:
[SoK: All You Need to Know About On-Device ML Model Extraction - The Gap Between Research and Practice](https://www.usenix.org/system/files/usenixsecurity24-nayan.pdf)
