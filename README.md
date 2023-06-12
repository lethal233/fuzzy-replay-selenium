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

In essence, this framework comprises two modules, as illustrated in Figure 4: pure replay and fuzzy replay. The browser add-on, Selenium IDE, is utilized to record manual interactions with web applications and export raw recording data during the recording phase. During playback, simple actions are executed by the engine, while complex actions prompt the framework to generate additional test cases mirroring the original's business logic, based on different strategies. These newly generated cases are then executed to extend the exploration states.

![image](https://github.com/lethal233/fuzzy-replay-selenium/assets/47763046/f560600a-12ef-4558-a071-94bd9051aa68)

## Pure Replay

The Pure Replay component serves two crucial purposes in web testing:

Regression Testing: This module incorporates a customized de-serializer that translates action lists, recorded using Selenium IDE, into Java objects. These objects include mouse click, mouse over, input, select, and more. Following the data pre-processing, the replay agent leverages Selenium WebDriver to execute the recorded sequences in the browser. Upon completion, it can generate comprehensive regression test reports.

Test Coverage Enhancement: The Pure Replay module facilitates human intervention, thereby improving test coverage. Since single-event-driven agents struggle to efficiently navigate complex actions, these actions can be recorded and factored into the potential action space during testing, augmenting the number of test inputs. Simultaneously, the present web state can be transitioned to a different strategic agent (e.g., random, BFS, Reinforcement Learning) to conduct further web exploration. For example, it can create a specific runtime environment, like an administrative login, facilitating deeper testing. The layout of the Pure Replay module is depicted in Figure 5.

![image](https://github.com/lethal233/fuzzy-replay-selenium/assets/47763046/621abb4c-7492-4d00-9bf5-27fe47f8330e)

## Fuzzy Replay

The 'Fuzzy Replay' module significantly eases the web testing workload by generating a wider range of inputs.

During web testing, we discovered that exploring new web states frequently entails similar manual operations, such as clicking on a web element followed by a cursor hover action. Manually recording all these interactions, particularly when they involve numerous similar operations on a page, is not only inefficient but also ineffective. In response, we capitalize on these operational similarities, using mutation operators to create a series of interactions that parallel the original's business logic. These newly generated interactions not only mitigate the monotony of repetitive tasks but also enhance the scope of automated exploration.

An intuitive approach involves identifying the elements {$e_i$} to be operated on from the recording list and locating corresponding similar elements (for instance, siblings) {$s_i$} at the same level for each element in {$e_i$}. This results in a set of similar interaction lists. Although this naive method serves as a rudimentary comparison to the fuzzy method, it's insufficient due to the diversity of web designs; simply identifying similar elements of {$e_i$} doesn't yield satisfactory results.

Hence, we introduce an improved error tolerance mechanism for element finding, building upon the naive method. Upon identifying similar elements, we employ diverse strategies to compile these elements into sequences. The locator generator subsequently crafts a unique XPath expression for each element and transforms them into action lists. A self-check submodule is incorporated to purge redundancy and invalidity from the combinations. After verification, the valid actions are routed to web automation agents. The design of the Fuzzy Replay module is showcased in Figure 6.

![image](https://github.com/lethal233/fuzzy-replay-selenium/assets/47763046/ba5816c4-0840-4411-b34e-f1c4980bddd5)

## Evaluation

Our record/replay framework, implemented with over 2,000 lines of code, relies on the Java Development Kit 11, Selenium WebDriver (version 4.1.3), Jsoup library (version 1.14.3), Selenium IDE (version 3.17.2), and Chrome along with its driver (version 100.0.4896.75). To demonstrate this work's efficiency and effectiveness, we conducted an empirical evaluation, investigating the following four research questions:

RQ1: What proportion of action sequences recorded by Selenium IDE can be successfully played back by this framework?
RQ2: How significantly does the fuzzy replay enhance test coverage?
RQ3: How much does the fuzzy replay improve test efficiency?

### Evaluation Setup and Results

To demonstrate the system’s effectiveness and its ease of use, we selected ten web applications accessible in China for the RQ1 evaluation process. These were chosen based on their ranking, average page size, and category (refer to Table 1). The ranking data is sourced from Alexa [22]. The average page size was determined by visiting ten random pages within this domain and calculating their average size.

During the experiment, we tested these web applications using SUSTech Wi-Fi on MacOS 12.3.1 and Windows 10 21H2. Additionally, in our fuzzy replay module, we empirically set the parameters (t_1, t_2) to (3,2).

|     Name           |     Domain             |     Ranking    |     Category         |     Avg. Page Size    |
|--------------------|------------------------|----------------|----------------------|-----------------------|
|     Baidu          |     baidu.com          |     1          |     Search Engine    |     0.4 KB            |
|     Bilibili       |     bilibili.com       |     2          |     Stream Video     |     137 KB            |
|     QQ             |     qq.com             |     4          |     Others           |     37 KB             |
|     CSDN           |     csdn.net           |     8          |     Blog             |     100 KB            |
|     Sina           |     sina.com.cn        |     11         |     Others           |     154 KB            |
|     163            |     163.com            |     12         |     Others           |     195 KB            |
|     JD             |     jd.com             |     13         |     E-commerce       |     308 KB            |
|     Sohu           |     sohu.com           |     14         |     Others           |     47 KB             |
|     HuaweiCloud    |     huaweicloud.com    |     /          |     Service          |     347 KB            |
|     SUSTech        |     sustech.edu.cn     |     /          |     Education        |     58 KB             |

For RQ1, we record ten random action sequences on the selected websites via Selenium IDE and compare the number of successful pure replays among Selenium IDE, exported JUnit, and this framework. The success of the playback is judged by whether there is any program exception or inconsistent interface state during the replay process.

For RQ2, we compare test coverage between naive (baseline) and our fuzzy method to find how much fuzzy replay can improve the web testing process. For each site shown in Table 2, we record three complex actions and feed those seeds into naive and fuzzy algorithms. Those generated sequences will then be played back to verify their validity and effectiveness. We collect the number of URLs explored via naive and fuzzy methods. Furthermore, we manually count the expected number of URLs to show the reliability of our algorithm. Furthermore, JavaScript coverage is also included to show the improvement of web dynamic code execution. Here, the JS coverage is calculated by c_js=B_executed/B_total, where B_executed,B_total respectively means the number of bytes of the executed JavaScript code and the total JavaScript code.

For RQ3, we verify the self-check submodule by comparing the total time cost and the ratio of valid operations in the generated mutants with the no self-check fuzzy module. The total time cost consists of generation, validation, and execution. We select part of the dataset in the RQ2. Here the average time is calculated by T_avg=(T_g+T_c+T_r)/N_v, where T_g,T_c,T_r respectively mean the time of generation, self-check, and replay, and N_v means the number of the generated interactions that can explore new web states (valid operations). We calculate success rate s by s=N_v/N_a, where N_a represents the number of operations after self-checking.

|     Site           |     Selenium IDE    |     Exported JUnit    |     The Framework    |
|--------------------|---------------------|-----------------------|----------------------|
|     Baidu          |     9/10            |     4/10              |     8/10             |
|     Bilibili       |     8/10            |     2/10              |     6/10             |
|     QQ             |     10/10           |     7/10              |     10/10            |
|     CSDN           |     9/10            |     9/10              |     10/10            |
|     Sina           |     9/10            |     8/10              |     10/10            |
|     163            |     10/10           |     6/10              |     10/10            |
|     JD             |     9/10            |     6/10              |     9/10             |
|     Sohu           |     7/10            |     7/10              |     9/10             |
|     HuaweiCloud    |     5/10            |     2/10              |     10/10            |
|     SUSTech        |     7/10            |     4/10              |     9/10             |
|     Total          |     83/100          |     55/100            |     91/100           |

|     Site           |     Visited URLs    |              |     JS Coverage    |               |
|--------------------|---------------------|--------------|--------------------|---------------|
|                    |     Baseline        |     Fuzzy    |     Baseline       |     Fuzzy     |
|     Baidu          |     13              |     20       |     0.1199         |     0.2292    |
|     Bilibili       |     36              |     46       |     0.0424         |     0.0811    |
|     QQ             |     28              |     52       |     0.0537         |     0.1411    |
|     CSDN           |     -               |     -        |     -              |     -         |
|     Sina           |     13              |     14       |     0.2081         |     0.1872    |
|     163            |     27              |     27       |     -              |     -         |
|     JD             |     3               |     62       |     0.3511         |     0.8806    |
|     Sohu           |     4               |     12       |     0.0182         |     0.6680    |
|     HuaweiCloud    |     4               |     357      |     0.2348         |     0.2332    |
|     SUSTech        |     99              |     108      |     0.1310         |     0.1318    |


|     Site           |     Visited URLs    |              |     JS Coverage    |               |
|--------------------|---------------------|--------------|--------------------|---------------|
|                    |     Baseline        |     Fuzzy    |     Baseline       |     Fuzzy     |
|     Baidu          |     13              |     20       |     0.1199         |     0.2292    |
|     Bilibili       |     36              |     46       |     0.0424         |     0.0811    |
|     QQ             |     28              |     52       |     0.0537         |     0.1411    |
|     CSDN           |     -               |     -        |     -              |     -         |
|     Sina           |     13              |     14       |     0.2081         |     0.1872    |
|     163            |     27              |     27       |     -              |     -         |
|     JD             |     3               |     62       |     0.3511         |     0.8806    |
|     Sohu           |     4               |     12       |     0.0182         |     0.6680    |
|     HuaweiCloud    |     4               |     357      |     0.2348         |     0.2332    |
|     SUSTech        |     99              |     108      |     0.1310         |     0.1318    |




