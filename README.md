# fuzzy-replay-selenium

This is a repo of my graduation thesis project at Southern University of Science and Technology.

## Introduction
The swift advancement of web technologies has driven the web to become a primary platform for various applications, causing a surge in the development of complex web applications. Ensuring their performance, reliability, and quality has become a significant focus post-development.

Testing these applications, built using complex business logic and multiple languages, is challenging. Web application testing typically comprises backend functional testing, API testing, and graphical user interface (GUI) testing. While the first two have evolved over time, GUI testing progress is stymied by unpredictability, element locator vulnerability, and ambiguity in defect criteria.

Existing automated web exploration tools like Selenium [2], WebdriverIO [3], Cypress [4], and Robot Framework [5], are single-event driven and struggle with complex multi-event operations. Manual methods, although more reliable, are time-consuming and labor-intensive.

This study proposes a robust record and replay framework to address these challenges. The framework is designed to accurately trigger recorded events and generate 'mutant' action sequences, mimicking human behavior for enhanced exploration and verification. The generated mutants expand the scope of automation agents and allow complex business logic interactions.

The framework was evaluated on ten real-world web applications, demonstrating robustness in replaying recorded sequences and successfully generating complex interactions that improved test coverage and efficiency.

## System Design

### Overall Design

In a nutshell, this framework consists of two components (shown in Figure 4): pure replay and fuzzy replay. The browser add-on Selenium IDE mentioned above is adopted to record the manual interactions with the web applications and export the raw recording data in the recording stage. In the playback stage, simple actions will be simply performed by the execution engine. As for complex actions, this framework will attempt to generate more test cases that share similar business logic with the original ones according to different strategies. These newly generated cases will be further executed to expand the exploration states.

![image](https://github.com/lethal233/fuzzy-replay-selenium/assets/47763046/f560600a-12ef-4558-a071-94bd9051aa68)
