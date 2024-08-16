# GSoC2024
![gsoc_cern_banner_flat](https://github.com/user-attachments/assets/a5055722-9b8f-4d02-a628-115dfa4e2fe3)
![rtemsorg300x300](https://github.com/user-attachments/assets/c392f825-505f-43c6-a2f2-3642b45a4228)


## Introduction
Hello! I'm Mohamed Hassan, currently pursuing a master’s degree in Robotics at The American University in Cairo. My passion lies in low-level software development, electronics, and embedded systems.

My journey into embedded systems felt like discovering my true calling. Recently, I’ve become fascinated by contributing to open-source projects, inspired by the idea of global collaboration.

I stumbled upon Google Summer of Code (GSoC) and found [RTEMS](https://summerofcode.withgoogle.com/programs/2024/organizations/rtems-project) (Real-Time Executive for Multiprocessor Systems) participating. Without hesitation, I wanted to join the RTEMS community to contribute to their awesome project.

I'm thrilled to share that I've been accepted into GSoC 2024 with RTEMS, an open-source real-time operating system used in critical applications, especially in the space industry—RTEMS has even orbited Venus, Mars, and the Sun!

My accepted [project](https://docs.google.com/document/d/1Kn02yQQNI9qHwup5kuGEhj-9l-dpnwYgYvFceXD-BxA/edit#heading=h.f0qomue69e9r) focuses on making the stack checker reporter configurable. Traditionally, RTEMS’ stack checker is initialized automatically with each task, and if a stack overflow occurs, it logs the task information and shuts down the system. My project aims to give users the flexibility to configure the response when a stack overflow is detected.

## Community Bonding
During the community bonding period, I engaged with the RTEMS community and focused on understanding the stack checker's error handler in depth. I created a mind map to visualize the functions and utilities provided by the stack checker.
<img width="12384" alt="Untitled" src="https://github.com/user-attachments/assets/326c92ac-f9fb-4663-b131-11ab2eb22a4c">
The stack checker code is organized into a few key files:
- `cpukit/libmisc/stackchk/check.c`: Contains the main function implementations.
- `cpukit/include/rtems/stackchk.h`: Header file necessary for installing the stack checker.
- `testsuite/libtests/stackchkx`: Contains three test cases covering all aspects of the stack checker.
This mind map helped me define the scope of my project and identify the areas I needed to focus on.

## Midterm Evaluation
This is the first part of my participation of GSoC 2024 which starts from the date of start of actual coding till the midterm evaluation. The start wasn't easy, since this is my first time participating in big project and also using `Git`. After some time trying to figure out how to start. I started by creating preparing my workspace by:
- Create a fork of RTEMS main gitlab repo: [https://gitlab.rtems.org/Hamzi/rtems](https://gitlab.rtems.org/Hamzi/rtems).
- Cloned my forked repo to my local machine.
- Create new branch to contains all my work by `git checkout -b feature/stack-reporter-config`.

Then, my main goal was to make the stack checker reporter configurable. Meaning that gives the user the ability configure which reporter function to be used in his application. The default reporter provided by RTEMS was [Stack_check_report_blown_task](https://gitlab.rtems.org/Hamzi/rtems/-/blob/5/cpukit/libmisc/stackchk/check.c?ref_type=heads#L247-292) which is called by [rtems_stack_checker_switch_extension](https://gitlab.rtems.org/Hamzi/rtems/-/blob/5/cpukit/libmisc/stackchk/check.c?ref_type=heads#L298-341).
I tackled down this by:
- `cpukit/include/rtems/confdefs/extensions.h`: I created a new macro check to configure which reporter to be selected based on the 
