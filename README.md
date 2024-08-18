# GSoC2024

![gsoc_cern_banner_flat (1)](https://github.com/user-attachments/assets/0ca29584-9d71-429d-bf8d-c63a91dbfc2d)
![rtemsorg300x160 1 (1)](https://github.com/user-attachments/assets/bf08a22f-5f2c-49ae-9535-714905126d3e)

## Introduction
Hello! I'm Mohamed Hassan, currently pursuing a master’s degree in Robotics at The American University in Cairo. My passion lies in low-level software development, electronics, and embedded systems.

My journey into embedded systems felt like discovering my true calling. Recently, I’ve become fascinated by contributing to open-source projects, inspired by the idea of global collaboration.

I stumbled upon Google Summer of Code (GSoC) and found [RTEMS](https://summerofcode.withgoogle.com/programs/2024/organizations/rtems-project) (Real-Time Executive for Multiprocessor Systems) participating. Without hesitation, I wanted to join the RTEMS community to contribute to their awesome project.

I'm thrilled to share that I've been accepted into GSoC 2024 with RTEMS, an open-source real-time operating system used in critical applications, especially in the space industry—RTEMS has even orbited Venus, Mars, and the Sun!

My accepted [project](https://docs.google.com/document/d/1Kn02yQQNI9qHwup5kuGEhj-9l-dpnwYgYvFceXD-BxA/edit#heading=h.f0qomue69e9r) focuses on making the stack checker reporter configurable. Traditionally, RTEMS’ stack checker is initialized automatically with each task, and if a stack overflow occurs, it logs the task information and shuts down the system. My project aims to give users the flexibility to configure the response when a stack overflow is detected.

## The Community Bonding Period
During the community bonding period, I engaged with the RTEMS community and focused on understanding the stack checker's error handler in depth. I created a mind map to visualize the functions and utilities provided by the stack checker.
<img width="12384" alt="Untitled" src="https://github.com/user-attachments/assets/326c92ac-f9fb-4663-b131-11ab2eb22a4c">
The stack checker code is organized into a few key files:
- `cpukit/libmisc/stackchk/check.c`: Contains the main function implementations.
- `cpukit/include/rtems/stackchk.h`: Header file necessary for installing the stack checker.
- `testsuite/libtests/stackchkx`: Contains three test cases covering all aspects of the stack checker.
This mind map helped me define the scope of my project and identify the areas I needed to focus on.

## The Midterm Evaluation
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
Towards the midterm evaluation, the main structure of my project was established. After thoroughly testing my work in my local repository, I proceeded to build the RTEMS project using the following commands:
Navigate to my local repository:
```
cd rtems
```
Set up the environment path:
```
export PATH=$HOME/rtems/rtems/6/bin:"$PATH"``
```
Verify the compiler installation:
```
command -v sparc-rtems6-gcc && echo "found" || echo "not found"
```
Configure the build:
```
cd $HOME/rtems/src/rtems
echo "[sparc/erc32]" > config.ini
echo "BUILD_TESTS = True" >> config.ini
./waf configure --prefix=$HOME/quick-start/rtems/6
```
Clean the build environment:
```
./waf clean
```
Build the project:
```
./waf
```
Next, I ran all the tests to ensure that the new test was functioning correctly:
Run all tests
```
rtems-test --rtems-bsp=erc32-sis build/sparc/erc32
```
Navigate to the executable file of the new test suite:
```
cd build/sparc/erc32/testsuites/libtests
```
Execute the test `stackchk03.exe`:
```
rtems-run --rtems-bsps=erc32-sis stackchk03.exe
```
After confirming that everything was working correctly, I pushed my work to my origin repository and created a merge request to include my changes in the RTEMS upstream repository. This process took some time as I focused on establishing a smooth workflow for my Git repository, ensuring that my commit history remained linear and easy to review. Below are the Git commands I used, which may be helpful to others:
Start at the main branch by pulling the latest updates from the upstream main repository:
```
git pull upstream main
```
Switch to the branch containing my work:
```
git checkout feature/stack-check-config
```
After completing the coding and making all necessary modifications:
```
git add .
```
Commit the changes.
```
git commit -m "commit message"
```
Fetch the latest updates to my feature branch:
```
git fetch upstream
```
Rebase my work on top of the latest updates from the upstream main repository:
```
git rebase upstream/main
```
You can view the changes I submitted just before the midterm evaluation period [here](https://gitlab.rtems.org/rtems/rtos/rtems/-/merge_requests/86/diffs?diff_id=2142&start_sha=0306a70f4366031e4c8dc5d0b1e4a25b6db60bdc#ba5d304e96c420f4fa6d1983a6f6d65bba76368f_103_99).

## The Second Phase 
During the first phase of GSoC, my primary focus was to make the stack checker reporter configurable. I achieved this by designing a conditional preprocessor macro in `confdefs` that allows users to select the reporter function based on their application's configuration. Users can choose the default RTEMS reporter, a quiet/basic reporter or their own custom reporter. To ensure robustness, I developed the `stackchk03` test case to validate the behavior of different reporter configurations, which worked as expected.

In the second phase, after optimizing and cleaning the code, I proposed changing the default reporter to a quiet reporter that only calls the fatal error handler in case of a stack overflow. This simplifies the default functionality, leaving advanced configurations to the user. The proposal was accepted, and I developed `stackchk04` testsuite to cover the full functionality of the stack checker. The testsuites now have the following structure:
- `stackchk` Tests the default quiet reporter.
- `stackchk03` Tests the user-defined custom reporter.
- `stackchk04` Tests the detailed reporter.
After finishing the main work I spend sometime improving the coding style. I updated the Doxygen documentations and cleaning up the code. Thankfully, the changes I proposed were successfully merged into the RTEMS codebase. I'm deeply grateful to my mentors, especially Joel, for his patience and guidance.

In the final days of the summer, I focused on documenting the changes introduced. I submitted two merge requests: one for adding documentation on the stack checker reporter configuration, available [here](https://gitlab.rtems.org/rtems/docs/rtems-docs/-/merge_requests/41) and [here](https://gitlab.rtems.org/rtems/prequal/rtems-central/-/merge_requests/4).

## The final Report

