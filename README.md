# M5-Stack-Covid-19-Symptoms-Detector

M5 COVID-19 Symptoms Detector
Shehryar Ahmed Subhani
Juan Diego Guevara
Ramsha Bilal
Contents
Introduction	
Project Development	
Software/Devices Used	
Other Resources	
Implementation Details	
User Interface Components	
Splash screen	
Main Screen	
Functionalities	
Cough Detection	
Questionnaire	
Covid Report	
Results and Evaluation	
Conclusion and Future work	
Reflection on Learning	

Introduction
The objective is to develop an M5 Stack COVID-19 Symptoms Detector that utilizes the M5 Stack Core 2’s functionalities to monitor for cough and detect COVID-19 symptoms, namely loss of taste or smell, sore throat, dry cough, fatigue, fever, tiredness, muscle pain, and chest pain. Upon detection of symptoms, it needs to report that a user is suspicious for COVID-19 so appropriate action can be taken such as covid testing, quarantine, or vaccination.
COVID-19 is a deadly disease that has 264 million cases worldwide. 5.2 million people have lost lives to this fatal pandemic. Unfortunately, these numbers continue to rise as COVID’s new Delta and Omicron variants spread rapidly around the globe. The disease, caused by a coronavirus called SARS-CoV-2, is contagious; hence, it can spread from one person to another through physical contact and/or through respiratory microdroplets exchanged while breathing, talking, coughing, or sneezing. Therefore, to stop the disease from spreading, it is imperative to identify anyone who has COVID symptoms so they can be isolated from people and get tested for COVID. This will ensure that someone with COVID will not spread it to other people. Thus, the objective of the project is to reduce the spread of COVID-19. To identify COVID-19 symptoms, we have developed the M5 Covid-19 Symptoms Detector which will monitor a person for common COVID symptoms. First, it will monitor surroundings for coughing using its built-in microphone. The cough detection will use a mathematical model we have designed to check if the cough duration exceeds a set threshold of 2. If yes, the device will pop up a questionnaire for the potential COVID19 patient to fill using the touch screen. It will ask the user to answer questions about any symptoms they may be facing and if the symptoms also exceed a set threshold of 3, the device will report that the user is suspicious for COVID. If the threshold is not exceeded, it will report that COVID is not detected. 
This device can be used in hotels, homes, and schools to closely monitor users for coughing and to detect other symptoms. For example, a guest is staying in a hotel room can be monitored, and, if found suspicious for cough, he/she can be isolated, tested, and vaccinated accordingly. This way, we aim to develop a device to detect potential COVID-19 patients and prevent the spread of COVID.
Project Development
Software/Devices Used
•	M5 Stack Core 2
•	Computer (as a server)
•	Python libraries 
o	micropython: m5stack, m5stack_ui, uiflow, easyIO, wifiCfg
o	aubio
Other Resources
•	cough dataset (recordings) from Virufy 
Implementation Details 
For implementation, we are using MicroPython to develop our code. The following components are developed and implemented through code:
 
User Interface Components
•	Splash screen
•	Main screen 
Splash screen
 
The splash screen is the display when the device is turned on. It shows the following:
I.	Title: Name of the device
II.	Subtitle
III.	Name of team members 
•	Vibration
The device is programmed to vibrate when the device is turned on along with the display of the splash screen. 
Main Screen 
The main screen has the following design and displays the time, battery, and indicate that symptom detection is in progress.
 
The main screen has a display of the battery percentage, time, and a message “detection in progress” with alternating dots, that will indicate to the user that symptoms detection is in progress. We also plan to add the wave pattern for the cough detected through the microphone.
The admin mode will show time, battery, and the status of the user i.e., COVID-safe (in green) or COVID-risk (in red). 
•	Battery Display and Vibrations
The main screen displays the battery percentage in one corner. If battery is below 20%, the battery percentage turns red and the device vibrates, indicating the need to charge the device. 
If battery is charging, the battery display turns green to indicate the charger is connected properly. 
•	Time Display
The main screen also shows the time and refreshes repeatedly to update the time, in case that time has changed. 
•	Bug Fix
Our device has a bug where sometimes, the time display shows 3 am when the actual time is not 3 am. We have introduced a bug fix in the code so this can be prevented. 
Functionalities
The purpose of the cough detection system is to monitor surroundings for noise, record the noise through M5 Stack’s microphone, and then process the audio recordings to detect cough. The mathematical model calculates if the detected cough per unit time is beyond the threshold of normal coughing and if yes, the program prompts a questionnaire to ask user for other symptoms. According to the answers, a covid report is made. 
Cough Detection
•	Audio Frequency Processing 
We take advantage of the m5 stack microphone to record surrounding noise. Our audio frequency processing program takes advantage of the frequency analysis functions incorporated in Aubio library. It analyses the fundamental frequency of a given audio every interval of time, called ticks.
We consider a cough to be in between 14 and 60 ticks and its frequency in between 300 and 900 hertz based on data collected through research. (CITE) This model is naturally constrained by the quality of the microphone and of the frequency analysis algorithm, and it relies on the fact that there has to be silence before and after the cough, which would for example simulate a hotel-room environment but not a club. 
If a sound between 14 and 60 ticks of frequency between 300 and 900 Hz is detected, the program reports it as a cough. 
•	Mathematical Model
The following model calculates if the cough detected is abnormal/above a “normal” threshold. It is based on the fact that a healthy person usually coughs a maximum of two times per hour. Let’s assume the algorithm of the M5COVID-19 detects cough with an accuracy of 100%. 
We use a decay model to determine when a person’s cough is sufficient to prompt them the covid Questionnaire. Our model will be a function that goes up every time a person coughs and then decays over time, until the function increases by another cough, and then decays again.
We divide time in sections of 5 minutes. If the program detects a cough in a section of 5 minutes, a counter c is incremented by one. The counter c does not increment in the same section again; it increases only if the person coughs again in a different section. Counter c has a half-life of 30 minutes. Hence the decay model is given by the equation:
 
Where m is the number of minutes passed after the last cough.
Assume a person coughs once every 30 minutes to clean their airways. As mentioned, this is the highest cough rate for a healthy person. Let’s analyze the model in this scenario and prove that f(c, m) approaches 2 asymptotically. We will use induction. 
We know for c = 1, f(1, 0) = 1 ≤ 2 and f(1, 30) = 1/2 ≤ 2. That's our base case. Now let's assume f(k, 0) and f(k, 30) are both less than 2. We will prove in the next cough, the function remains less than 2
Note 
 
Since f(k, 0) is less than two (by hypothesis), then ½[f(k, 0)] = f(k, 30) is less than 2/2 = 1.
Finally, because a person cough again after 30 minutes
 
So, immediately after a cough, the function is still less than two. Since it is a decay mode, f( f(k, 30) + 1, 30) < f( f(k, 30) + 1, m) < f( f(k, 30) + 1, 0) < 2. So, f(c, m) will always be less than 2, as was to be shown.
This gives us a very natural threshold to trigger the COVID Symptom form. If f(c, m) goes beyond 2, it will prompt the user for the questionnaire. However, to account for errors of the algorithm, the threshold will be increased to 2.25. 
Questionnaire 
Once the device detects that the cough is beyond the set threshold, it is triggered to pop open a questionnaire for the user to answer. This questionnaire is to ask for other symptoms the user may be experiencing. To determine which symptoms to include, we performed research, and according to WHO and as shown in the diagram below, some of the most common symptoms include fatigue, shortness of breath, sore throat, dry cough, loss of taste and smell, muscle pain, fever, watery eyes, and in worse cases, chest pain and difficulty breathing. 
 
The following questionnaire has been developed with the help of a questionnaire from the NYUAD Health Centre:
 
In each question, user will answer yes, no, or maybe, using the three touch buttons on the device. Based on these answers, a report is created. 
Covid Report 
The answers are analyzed using the following logic. A counter is set to 0. If the user answers a yes, the counter is incremented by 1. If the user answers a maybe, the counter is incremented by 0.5. This is repeated for each question. Once the counter exceeds the value of 3, the user is declared to be at risk of covid.  The value 3 is chosen because it means the user has either three of the symptoms for sure (3 yes, 1 maybe) or has some symptoms (1 yes, 5 maybes). Even if there is 1 yes, 5 is sufficiently large to ensure there are sufficient maybes for the threshold to exceed. If the counter does not exceed 3, the device reports that Covid is not found. 
Results and Evaluation
Challenges
Audio file transfer from M5Stack to PC:
•	We tried Micropython exclusive modules such as EzData and Remote+ but they were limited to only sending strings.
•	We tried console-based libraries such as Ampy but it would not reliably connect with the M5.
•	We tried transmission methods such as UDP and TCP, but the server never received requests from the client.
Conclusion and Future work
Achievements
Through our program we were able to meet our goal of developing both a cough detection system, a symptoms questionnaire, and a report. We were able to develop a user-friendly, easy-to-use interface as well that allows user to easily monitor their status.
The project was able to record voices, detect coughs in them, and indicate if cough is beyond a threshold. Thus, assuming the file transfer works, the program will trigger the questionnaire when cough exceeds the set threshold. The questionnaire is also successful in reporting when a patient’s symptoms exceed a set threshold. 
Future developments 
The only major limitation to our project, as discussed above, was the audio file transfer from the M5 Stack device to a PC. Hence, this can be achieved in the future. 
This project is flexible and can further be expanded to add a proximity feature too. If a COVID-risk user comes near COVID-safe users, it will send a risk message to the other users so they can minimize contact with the user at risk. 
Additionally, the COVID report made in the end can be sent to an administration, control system, or database as well to keep record of who is suspicious for COVID and to take actions as needed.
Furthermore, the mathematical model can be optimized more for increased accuracy. Artificial intelligence can be used to detect covid coughs more accurately. The questionnaire can also be updated to add different weight values to each symptom depending on how significant the symptom is. Finally, other COVID factors such as oxygen saturation and heart rate and body temperature can also be monitored with the help of external hardware such as thermal sensor. 
Reflection on Learning
Skills developed
We as a team were able to develop time management and organizational skills. We scheduled regular meetings and discussed the progress of our project which helped us develop important team working habits that are an essential part of professional life. 
We are now able to acknowledge that different members in a team bring different perspectives to the table and each perspective needs to be heard and given a fair chance. That is essential to a successful project. For example, in the beginning we all pitched different ideas and then agreed upon the one we found most impactful. 
During development of our code, we valued each other’s opinions. In times when we had different ideas and could not agree on one, we would spend time individually to work on those ideas and then meet to see which works best. For example, we simultaneously worked on incorporating Artificial Intelligence for cough detection as well as developing a cough detecting algorithm as a backup option because we realized that everything does not go as planned and it is important to have a backup. This way, we were able to manage the cough detection algorithm when the AI for cough detection could not be set up. 
Moreover, we divided tasks according to each person’s abilities and strengths. Moreover, we were able to work on our weaknesses too by learning from each other. For example, I was able to learn python from Shehryar and Juan who were already familiar with the language. 
We had the opportunity to work in python and learn it on top of the C++ taught in class. This way, the project allowed us to translate our programming skills from one language to - another which is a very important skill for programmers. 
By working in a team and through the presentation, we were also able to work on our communication skills. Since the project required a lot of independent work and research, we were able to polish our research skills as well for example when looking for literature on coughing or when looking for sample codes and videos to see how the M5 core works. 
As a whole, the project proved to be a great avenue for learning and growth. 
