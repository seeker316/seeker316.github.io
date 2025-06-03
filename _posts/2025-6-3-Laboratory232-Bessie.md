---
layout: post
title: Laboratory 232 - Bessie
subtitle: LAB 232 blog I - Building Bessie, the Roadbot. 
header-img: img/bessie/bessie_blog_cover.jpeg
---

It was in my second year of engineering, I found out we had this subject called the "mini-project". In this, we were required to form teams and work on a project throughout the semester. As the whole class scrambled to find good partners, I ended up with a group of individuals—[Abhinav](abhinav.shetti@gmail.com), [Dhiraj](dhirajmehta2323@gmail.com), and [Nandini](bhagatnandini07@gmail.com)—who shared the same kind of curiosity and way of thinking. People who appreciated the effort it took to get things going and were always ready to dive in. I don’t buy into destiny, but if it were real, being on this team was definitely meant to happen. I can’t imagine engineering without them—never in this life or any other. 

Without wasting much time, we dove into searching for problems to solve and ideas to innovate. During our brainstorming sessions, we came up with an idea: to create an automated solution for fixing potholes on roads. We realized it was a persistent problem, and there was no effective solution being implemented in a timely manner. We began building a scaled-down prototype to test the concept. We started scavenging for materials and information wherever we could—around the college and from anyone willing to help, we were total noobs, barely a few months into learning Arduino. We designed a CAD model of the prototype and worked through its basic mechanics. For the pothole volume analysis, we came up with a makeshift solution using image processing, and—funny as it sounds—an ultrasonic sensor array.

![map grid](/img/bessie/pothole_analyzer.jpeg)

One fine day, while we were just getting into the groove of our project, we were introduced to the E-Yantra Robotics Competition by some of our seniors, who had, in fact, won it the previous year (2021–2022). At the time, we were hesitant. The idea of participating in a national-level robotics competition felt a bit out of reach for us, especially as beginners. We figured it was better to stay focused on our own project than risk an embarrassing failure.

Eventually, we caved, With half the department buzzing about E-Yantra, we decided to check it out ourselves. The E-Yantra Robotics Competition (e-YRC) features various themes, each representing a different problem statement from which participating teams must choose one to solve. As we went through the various themes listed for that year, one in particular caught our eye—_Functional RoadBot_. To our surprise, it aligned almost perfectly with the very problem we had already been working on. It felt surreal. Suddenly, the idea of competing didn’t seem as far-fetched anymore.
With very little idea of what the competition actually expected from us, we went ahead and registered for the competition, paying ₹1200—a discounted fee thanks to both an early bird offer and our college being part of the E-Yantra Lab Setup Initiative.

Our theme had 6 tasks split into 2 stages. Stage 1 focused on algorithm development (solving problems in code), while Stage 2 involved implementing solutions on robotic kits for shortlisted teams. The tasks in Stage 1 were:

- *Task 0*: Set up required software (Ubuntu on VirtualBox with Erlang VM) and run a test script without errors.
    
- *Task 1A*: Develop two functions for the Sum of Subsets problem: `sum_of_one` to find all subsets of an array summing to a given integer, and `sum_of_all` to find subsets matching sums from a 2D matrix.
	
- *Task 1B*: Use Tesseract OCR to detect numbers and their locations in a 3x3 grid image and output the numbers in this grid with their cell locations.
	
-  *Task 2A*: Develop an algorithm to fill a pothole of a given size using the available materials as efficiently as possible. Find the subset of materials that sum to the pothole size (or as close as possible without exceeding it).   
  Example : For a pothole size of 18 and materials {3, 5, 9, 4, 7, 1, 1}, the optimal subset is {9, 5, 4}.
	
-  *Task 2B*: Implement an algorithm to find the shortest path between two points on a grid map.
  Example : For the given grid, the shortest path from cell 9 to cell 2 is:  9 → 6 → 5 → 2.

![map grid](/img/bessie/task2b.jpeg)

With these tasks stage 1 was concluded and only 30 teams were shortlisted from the total of 376 teams.

In Stage 2, the hardware setup included a Raspberry Pi 3B paired with an OV5647 camera, a card reader with an SD card, and an AlphaBot2 platform by Waveshare Electronics, powered by Li-ion batteries.

![alphabot2](/img/bessie/alphabot2a.png)

We began by printing the arena on a flex sheet. In the subsequent tasks, we assembled the robot, installed the necessary software, and performed basic maneuvers, such as moving forward, reversing, and rotating.

![arena](/img/bessie/arena.png)

In *Task 3*, we focused on establishing parallel processing to improve efficiency and reduce code runtime. We were provided with a Raspberry Pi 3B, which has two processing cores. Parallel processing ensures optimal utilization of both cores. We achieved this using **GenServer** in Elixir, a concurrent programming model that manages state, handles multiple processes, and ensures smooth communication between them.

*Task 4* was the coolest—we could finally see everything we had worked on starting to fit together. For Task 4A, we had to develop a dispensing mechanism capable of selecting which dice to dispense into the deposition container. The dice came in different colors, with each color representing a specific value. For a given pothole size in the arena, we used the optimal subset algorithm developed in Task 1 to determine the most efficient combination of dice values to fill the pothole. Based on this, the corresponding colored dice were dispensed into the pothole.  

![dispenser mechanism](/img/bessie/dispensermech.jpeg)

We employed a rack and pinion mechanism for the linear actuation of the dispenser. The dice were arranged in cupboard-like stacks, from which a servo motor mounted at the end of the mechanism arm pushed the required dice out of the stack.

For *Task 4B*, we had to follow a white line to navigate through the arena map—an essential requirement for autonomous navigation. To achieve line following, we used a fundamental concept from control theory: **PID Control**. As part of the challenge, we had to traverse various sections, including S-shaped turns and a particularly difficult heart-shaped curve. Since it was our first time tuning PID values, it was a total nightmare—but also kind of fun in hindsight. Definitely one of the most memorable parts of the project.

For *Task 5*, we had to integrate all the algorithms written till Task 4 in order to complete the theme implementation in parts. Each system like line following, navigation, and the  dispensing mechanism had to work successfully on its own.

In *Task 6*, we were given just 48 hours to fully integrate all hardware and software systems for a complete, full-scale run. This task required us to carry out the entire pothole-filling process in the least possible time. Based on performance in this round, four finalists were selected to present their robots in the finals. It was truly a rush hour for us—critical decisions and innovative changes were made during this phase. One such improvement was the addition of a front-facing mirror placed at a slanted angle, allowing the camera to view both the stacked dice and the red nodes on the map simultaneously.

As the robot came together, we chose the perfect name—Bessie—after the trusty construction vehicle from [*Cars*](https://en.wikipedia.org/wiki/Cars_(film)), a childhood favorite for our generation.

All the hard work we had put in over the past six months finally paid off when we received the email confirming our selection for the finals. The game had just gotten tighter—we were now competing with the top three teams from across the nation. The race to perfect Bessie, had officially begun. We focused on refining task execution sequences, optimizing code, and minimizing delays. To ensure Bessie could operate effectively in any map configuration, we cut out the grid sections and rearranged them in a jumbled layout. 

Aesthetics were also a priority; we didn’t want Bessie to look like just another DIY project. I still remember the moment we removed the delays in the dispensing mechanism—watching the system run at full speed, Bessie looked like a cowboy slinging shots.

![dispenser mechanism](/img/bessie/aesthetics.jpeg)

And then came the finale. It was held over two days, on the 30th and 31st of March 2023. Three teams competed on Day 1, and the remaining four on Day 2. It was a great experience to be at the IIT Bombay campus during this time. We saw the other theme finalists and their robots, and it was very exciting to see the diverse solutions each team had developed for their problem statements. Even within the same theme, the robots were quite different.

![Finals at IIT](/img/bessie/finalday.png)

On Day 2, among the four finalists, we were the first to present. Watching Bessie perform in the finals was nerve-wracking, with all our hard work condensed into just a few minutes. After the round, the judges asked us questions about our experience, learning, and the decisions we made throughout the competition. In the end, all the effort paid off—we jointly secured 2nd Prize in the Functional Roadbot Theme of e-YRC 2022–23 alongside participants from NIT Calicut. It was a very close call and still feels unbelievable.

This competition became a benchmark for us and a strong testament to how well our team worked together, proving that we could take on more challenging and technically demanding projects. The confidence we gained from the e-Yantra competition pushed us to take on bigger and more ambitious projects. We continued working, learning, and enjoying every bit of it — all rooted in the shared hours, ideas, and chaos of **LAB 232**.

