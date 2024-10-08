# Google Summer of Code 2024 (GSoC 2024)

   ![2](https://github.com/user-attachments/assets/35894b5c-dada-45de-9e51-39e7508d660e)
   ![1](https://github.com/user-attachments/assets/2b749b0a-2ba1-42ba-9fbf-455e42d88ab4)

## Description
**Organisation**: Real-Time Executive for Multiprocessor Systems (RTEMS)

**Project Title**: Make Stack Checker Error Handler Configurable

**Contributor**:  Mohamed Hassan <muhammad.hamdy.hassan@gmail.com>

**Mentor**: Joel Sherrill, Gedare Bloom.

## Introduction
Hello! I'm Mohamed Hassan, currently pursuing a master’s degree in Robotics at The American University in Cairo. My passion lies in low-level software development, electronics, and embedded systems.

My journey into embedded systems felt like discovering my true calling. Recently, I’ve become fascinated by contributing to open-source projects, inspired by the idea of global collaboration.

I stumbled upon Google Summer of Code (GSoC) and found [RTEMS](https://summerofcode.withgoogle.com/programs/2024/organizations/rtems-project) (Real-Time Executive for Multiprocessor Systems) participating. Without hesitation, I wanted to join the RTEMS community to contribute to their awesome project.

I'm thrilled to share that I've been accepted into GSoC 2024 with RTEMS, an open-source real-time operating system used in critical applications, especially in the space industry, RTEMS has already been part of missions to Mercury ([BepiColombo](http://www.rtems.com/node/83)) , the asteroid belt ([DAWN](https://x.com/RTEMS_OAR/status/1573410179278045185) and [DART](https://mobile.x.com/RTEMS_OAR/status/1570102995416662019)), and even around Jupiter (JUNO).

My accepted [project](https://docs.google.com/document/d/1Kn02yQQNI9qHwup5kuGEhj-9l-dpnwYgYvFceXD-BxA/edit#heading=h.f0qomue69e9r) focuses on making the stack checker reporter configurable. Traditionally, RTEMS’ stack checker is initialized automatically with each task, and if a stack overflow occurs, it logs the task information and shuts down the system. My project aims to give users the flexibility to configure the response when a stack overflow is detected.

## Work Summary
For those who are TLDR. Here's a summary of my work this summer. 

My work focused on enabling systems to use a custom stack checker error handler, providing users with the flexibility to incorporate specific features as needed.

### Project Challenges
- **Getting Started with Git**: Initially challenging, managing commits in a large project taught me valuable lessons in version control.
- **Navigating the RTEMS Build System**: The manual build process was new to me, and it took time to master.
- **Creating Configurable Macros**: Designing flexible preprocessor macros required careful implementation to ensure compatibility with RTEMS.
- **Managing Multiple Dependencies**: Handling interconnected inclusions and dependencies required careful attention to detail and understanding the broader impact of changes.
- **Testing Robustness**: Developing comprehensive test cases for various reporter configurations was both challenging and crucial.

### Accepted Merge Requests
- **Configurable Stack Checker Reporter**: [Merge Request !86](https://gitlab.rtems.org/rtems/rtos/rtems/-/merge_requests/86)
- **New Test Cases for Configurable Stack Checker**: [Merge Request !161](https://gitlab.rtems.org/rtems/rtos/rtems/-/merge_requests/161)

### Ongoing Merge Requests
- **Documentation Updates**: [Merge Request !41](https://gitlab.rtems.org/rtems/docs/rtems-docs/-/merge_requests/41)
- **Specification Updates**: [Merge Request !4](https://gitlab.rtems.org/rtems/prequal/rtems-central/-/merge_requests/4)

### Code Merged Upstream
- **Making Stack Checker Reporter Configurable**: [View Commit](https://gitlab.rtems.org/rtems/rtos/rtems/-/commit/dc123bb828251b4e567390e9b6cfcae48af967b4)
- **Adding Test Cases for Configurable Stack Checker**: [View Commit](https://gitlab.rtems.org/rtems/rtos/rtems/-/commit/6ab27bb54e5c056ca7ef605f79fffc7869d1ce9b)

The following sections contains details about my project with GSoC & RTEMS during the summer.

## The Community Bonding Period
During the community bonding period, I engaged with the RTEMS community and focused on understanding the stack checker's error handler in depth. I created a mind map to visualize the functions and utilities provided by the stack checker.
<img width="12384" alt="Untitled" src="https://github.com/user-attachments/assets/326c92ac-f9fb-4663-b131-11ab2eb22a4c">
The stack checker code is organized into a few key files:
- `cpukit/libmisc/stackchk/check.c`: Contains the main function implementations.
- `cpukit/include/rtems/stackchk.h`: Header file necessary for installing the stack checker.
- `testsuite/libtests/stackchkx`: Contains three test cases covering all aspects of the stack checker.

This mind map helped me define the scope of my project and identify the areas I needed to focus on.

## The First Phase
This section covers the first phase of my participation in GSoC 2024, from the beginning of actual coding up to the midterm evaluation. As this is my first time working on a large project and using `Git`, the start was challenging. However, after some initial efforts to find my footing, I was able to set up my workspace as follows:
- Forked the RTEMS main GitLab repository: [https://gitlab.rtems.org/Hamzi/rtems](https://gitlab.rtems.org/Hamzi/rtems).
- Cloned my forked repository to my local machine.
- Created a new branch to contain all my work using the command ```git checkout -b feature/stack-reporter-config```.

My primary goal was to make the stack checker reporter configurable, allowing users to configure which reporter function - either provided by RTEMS or thier own - to use in their applications. The default reporter provided by RTEMS was [Stack_check_report_blown_task](https://gitlab.rtems.org/Hamzi/rtems/-/blob/5/cpukit/libmisc/stackchk/check.c?ref_type=heads#L247-292) which is invoked by [rtems_stack_checker_switch_extension](https://gitlab.rtems.org/Hamzi/rtems/-/blob/5/cpukit/libmisc/stackchk/check.c?ref_type=heads#L298-341).
To achieve this I:
- Renamed `Stack_check_report_blown_task` to `rtems_stack_checker_reporter_print_details` and made it a public function.
- Created a new reporter function called `rtems_stack_checker_reporter_quiet` which only call fatal error handler.
- Updated the configuration macro in`cpukit/include/rtems/confdefs/extensions.h`to allow users to configure the desired reporter based on their application needs.
- Developed a new test case `testsuite/libtests/stackchk03` to verify that the application uses the correct reporter based on the user’s configuration.
- Edited grp.yml in `rtems/spec/build/testsuites/libtest` to include the new testsuite in the build process.
- Added `stackch03.yml` file for the new test case in the same path above.

Towards the midterm evaluation, the main structure of my project was established. After thoroughly testing my work in my local repository, I proceeded to build the RTEMS project using the following commands:

Navigate to my local repository:
```
cd rtems
```
Set up the environment path:
```
export PATH=$HOME/rtems/6/bin:"$PATH"
```
You can change that path to you actual path.

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
Then finally push the changes to the origine repository
```
git push -f origin feature/stack-check-config
```
After that I created a draft merge request.

You can view the changes I submitted just before the midterm evaluation period [here](https://gitlab.rtems.org/rtems/rtos/rtems/-/merge_requests/86/diffs?diff_id=2142&start_sha=0306a70f4366031e4c8dc5d0b1e4a25b6db60bdc#ba5d304e96c420f4fa6d1983a6f6d65bba76368f_103_99).

## The Second Phase 
During the first phase of GSoC, my primary focus was to make the stack checker reporter configurable. I achieved this by designing a conditional preprocessor macro in `confdefs` that allows users to configure the reporter function based on their application's configuration. Users can choose the default RTEMS reporter, a quiet/basic reporter or their own custom reporter. To ensure robustness, I developed the `stackchk03` test case to validate the behavior of different reporter configurations, which worked as expected.

In the second phase, after optimizing and cleaning the code, I proposed changing the default reporter to a quiet reporter that only calls the fatal error handler in case of a stack overflow. This simplifies the default functionality, leaving advanced configurations to the user. The proposal was accepted, and I developed `stackchk04` testsuite to cover the full functionality of the stack checker. The testsuites now have the following structure:
- `stackchk` Tests the default quiet reporter.
- `stackchk03` Tests the user-defined custom reporter.
- `stackchk04` Tests the detailed reporter.
After finishing the main work I spend sometime improving the coding style. I updated the Doxygen documentations and cleaning up the code. Thankfully, the changes I proposed were successfully merged into the RTEMS codebase. I'm deeply grateful to my mentors, especially Joel, for his patience and guidance.

In the final days of the summer, I focused on documenting the changes introduced. I submitted two merge requests: one for adding documentation on the stack checker reporter configuration, available [here](https://gitlab.rtems.org/rtems/docs/rtems-docs/-/merge_requests/41) and [here](https://gitlab.rtems.org/rtems/prequal/rtems-central/-/merge_requests/4).

## The Final Report

And here we are, at the close of an incredible summer journey! This season, I had the opportunity to work on a [project](https://summerofcode.withgoogle.com/myprojects/details/li0Ns91L) which aims to gives the user the ability to configure the stack checker error handler and determine it's behavior based on their own requirements.

focused on giving users the ability to configure the stack checker error handler, allowing them to tailor its behavior to their specific needs.

I developed a method that enables users to configure the stack checker reporter in their applications, choosing from three options: a basic quiet reporter, a detailed reporter, or their own custom reporter. Alongside this, I created test cases for each type of the reporters to ensure everything works smoothly.

Currently, I'm still in the process of updating the RTEMS documentation to reflect the new features added during this project. The documentation is nearly complete and will be merged soon.

You can check out the approved merge request for the configurable stack checker reporter [here](https://gitlab.rtems.org/rtems/rtos/rtems/-/merge_requests/86), as well as the merge request for the new test cases [here](https://gitlab.rtems.org/rtems/rtos/rtems/-/merge_requests/161). My contributions to the RTEMS upstream repository can be viewed [here](https://gitlab.rtems.org/rtems/rtos/rtems/-/commit/dc123bb828251b4e567390e9b6cfcae48af967b4) and [here](https://gitlab.rtems.org/rtems/rtos/rtems/-/commit/6ab27bb54e5c056ca7ef605f79fffc7869d1ce9b).

There's still ongoing work, including two merge requests for updating the [documentations](https://gitlab.rtems.org/rtems/docs/rtems-docs/-/merge_requests/41) and [specifications](https://gitlab.rtems.org/rtems/prequal/rtems-central/-/merge_requests/4).

Reflecting on this amazing summer, I’m incredibly grateful for the opportunity. I’ve learned so much, especially during the challenging early days. With the support of my mentors, I made significant progress and gained invaluable experience. This was my first time working on such a large project with numerous dependencies. The manual build process was new to me, and I also learned how to use Git the right way despite a few mishaps with my commit history! Most importantly, I overcame my initial fears and hesitations, thanks to my mentor's friendly and encouraging guidance. I can’t thank him enough. Thank you Joel!

I’m excited to continue being an active member of the RTEMS community and to contribute to many more exciting projects. Who knows, maybe one day my code will be part of a mission exploring Mars, Jupiter, and beyond!


