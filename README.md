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
This section covers the first phase of my participation in GSoC 2024, from the beginning of actual coding up to the midterm evaluation. As this is my first time working on a large project and using `Git`, the start was challenging. However, after some initial efforts to find my footing, I was able to set up my workspace as follows:
- Forked the RTEMS main GitLab repository: [https://gitlab.rtems.org/Hamzi/rtems](https://gitlab.rtems.org/Hamzi/rtems).
- Cloned my forked repository to my local machine.
- Created a new branch to contain all my work using the command ```git checkout -b feature/stack-reporter-config```.

My primary goal was to make the stack checker reporter configurable, allowing users to select which reporter function to use in their applications. The default reporter provided by RTEMS was [Stack_check_report_blown_task](https://gitlab.rtems.org/Hamzi/rtems/-/blob/5/cpukit/libmisc/stackchk/check.c?ref_type=heads#L247-292) which is invoked by [rtems_stack_checker_switch_extension](https://gitlab.rtems.org/Hamzi/rtems/-/blob/5/cpukit/libmisc/stackchk/check.c?ref_type=heads#L298-341).
To achieve this I:
- Renamed `Stack_check_report_blown_task` to `rtems_stack_checker_reporter_print_details` and made it a public function.
- Created a new reporter function called `rtems_stack_checker_reporter_quiet` which only call fatal error handler.
- Updated the configuration macro in`cpukit/include/rtems/confdefs/extensions.h`to allow users to select the desired reporter based on their configuration.
- Developed a new test case `testsuite/libtests/stackchk03` to verify that the application selects the correct reporter based on the user’s configuration.
- Edited grp.yml in `rtems/spec/build/testsuites/libtest` to include the new testsuite in the build process.
- Added `stackch03.yml` file for the new test case in the same path above.
Towards the midterm evaluation, the main skeleton of my project was set. Then After testing my work on my local repository I built the RTEMS project by using the following commands:
- Go to my local repository `cd rtems`
- ```export PATH=$HOME/rtems/rtems/6/bin:"$PATH"```
- ```command -v sparc-rtems6-gcc && echo "found" || echo "not found"```
- ```cd $HOME/rtems/src/rtems```
- ```
  echo "[sparc/erc32]" > config.ini
  echo "BUILD_TESTS = True" >> config.ini
  ./waf configure --prefix=$HOME/quick-start/rtems/6```
- ```./waf clean```
- ```./waf```
- Then to run all test and make sure that the new test runs correctly ```rtems-test --rtems-bsp=erc32-sis build/sparc/erc32```
- then I went to the executable file of my new testsuite ```cd build/sparc/erc32/testsuites/libtests```
- Finally to execute `stackchk03.exe` I run the command ```rtems-run --rtems-bsps=erc32-sis stackchk03.exe```

