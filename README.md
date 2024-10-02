COMP3301 2024 - Assignment 2
1. Introduction to Curriculum and Assignments
Lesson: COMP3301
Job published: 2024
Deadline: 3 p.m. Monday of Week 10 (September 30)
Submissions: Blackboard (reflection document) and Git. Code submissions will be graded in a practical session in week 10
1.1 Academic Integrity
All assessments are individual tasks. It is possible to discuss with your classmates the relevant aspects of C programming and the assessment specification, but you should not help each other in the actual coding process. Don't allow you to view other people's code, or allow your code to be seen or shared by others in any way. Submitted code will be checked for plagiarism and collusion, and a formal misconduct handling process will be initiated if a violation is found.
Use of AI tools: All assessment tasks assess students' abilities, skills, and knowledge without the help of generative artificial intelligence (AI) or machine translation (MT). The use of AI technology to generate answers, such as code generation, is strictly prohibited and may constitute student misconduct.
1.2 Background of the operation
The two processes can communicate through file descriptors (e.g., pipes, open files) or shared memory (e.g., used and mapped memory). However, file descriptors or shared memory can only be inherited if the process is parent-child.mmap()MAP_SHARED
UNIX allows a socket control message to be passed between any two processes using a socket control message via a UNIX domain socket. Shared memory can also be implemented via socket control messages, but this is not currently implemented in OpenBSD.SCM_RIGHTS
2. Overview of the job task
Understand SCM_RIGHTS: Find where to implement in the kernel and understand how it works, fill out and submit a flowchart, and explain the steps to find the relevant code and the tools used in the reflection document.SCM_RIGHTS
Implementation SCM_PAGES: Implement an inter-process communication mechanism that shares in-memory pages via a socket-controlled message interface, similar to. Instead of an array of file descriptors, an array of structures is sent, and a variety of boundary cases are handled.SCM_RIGHTSmemshareblk
Identify security risks: Identify at least one potential security risk related to task design or code implementation in the reflection document, and explain the risk in detail and how to mitigate that risk.
3. Steps to complete the job
3.1 Preparation
Download the patch for the base code
Command:cd ~ ftp https://stluc.manta.uqcloud.net/comp3301/public/2024/comp3301 -2024 -a2.patch
Create an A2 branch
Command:cd /usr/src git checkout -b a2 openbsd -7.5
Apply the base code patch
Command:git am < ~/comp3301 -2024 -a2.patch
Install the header file
Command:cd /usr/src/include doas make includes
Build the kernel
Command:
cd /usr/src/sys/arch/amd64/compile/GENERIC.MP
make obj
make config
make -j4
doas make install
Reboot the system
Command:doas reboot
Build and install a2client
Command:
cd /usr/src/usr.sbin/a2client
make obj
make doas make install
Build and install a2server
Command:
cd /usr/src/usr.sbin/a2server
make obj
make doas make install
Optionally, build and installfdsend
Command:
cd /usr/src/usr.sbin/fdsend
make obj
make doas make install
Optionally, build and installfdrecv
Command:
cd /usr/src/usr.sbin/fdrecv
make obj
make doas make install
3.2 Specific tasks
Understand SCM_RIGHTS
Read and understand how it is implemented in OpenBSD 7.5 and how file descriptors are passed between processes.SCM_RIGHTS
Fill out and submit the flowchart template provided on Blackboard, explaining in the reflection document the steps to find the relevant code in the kernel source code and the tools used (e.g., etc.).grep/fgrepcscope
Implement SCM_PAGES
The base code patch definition, structure, and flags in some header files.SCM_PAGESstruct memshareblk
Implement an interface that sends an array of structs instead of an array of file descriptors. Each structure represents a contiguous list of in-memory pages to be shared or received, and needs to handle a variety of boundary cases, such as an address that does not point to the start of the page, a page that is not mapped, a gap in the list of pages, a sender unmapping a page before the receiver receives it, and a sender exits before the receiver receives it.SCM_PAGESmemshareblkmemshareblk
Identify security risks
Identify at least one potential task-related security risk, such as denial of service (operating system crash, performance degradation), leakage of confidential information, leakage of kernel data structures into user space, etc., in the reflection document, and explain the risk in detail and how to mitigate the risk.
4. Job Requirements
4.1 Code Style
The code is written in accordance with the OpenBSD style guide, which can be checked for style violations using the tool. The code style score will be calculated based on the number of style violations in the code you wrote or modified, and style violations in the OpenBSD source tree or underlying code do not affect the score.https://stluc.manta.uqcloud.net/comp3301/public/2022/cstyle.pl
4.2 Reliability, robustness, portability, and modularity
The code should be reliable and robust, able to handle all errors correctly, and should not crash unexpectedly. It should be portable and modular, constants such as page size should not be hardcoded, and similar code should not be repeated in multiple places.
4.3 Compilation
The code must be able to compile in a (general-purpose multiprocessor) configuration of the AMD64 architecture. Code with compile-time errors is manually graded and can result in significant penalty points.GENERIC.MP
5. Git Commit Requirements
Submit Where: Commit and push your changes to the Git repository provided by the previous course.source.eait.uq.edu.au
Submissions: Only source code and makefiles should be submitted, and outputs, core dumps, or patch files for the underlying code should not be submitted.cscope
a2 branch content: should include OpenBSD 7.5 base commits, A2 base patch commits, and commits that add the required functionality yourself. Submission history and submission messages do not affect grades, but it is recommended to submit frequently and use appropriate submission messages.
5.1 Reflection Documentation
Content Requirements:
Complete the flowchart.SCM_RIGHTS
Identify and describe at least one security risk. The reflection document should contain sections such as an introduction, a flowchart (to complete the A or B template), security risks, and conclusions, with the possibility of adding additional sections or subsections.
Format requirements: Upload as a PDF file, the file name is student number, and the number of pages is up to 4 pages.a2.pdf
5.2 Scoring Method
Presentation Grading: Submissions will be graded by course staff in face-to-face presentations with students during practice classes during the closing week. Students must attend in person, or submissions will not be graded.
Presentation: In the presentation, students will be asked to present an assignment that includes explaining how it works, where to find the relevant code, why the flowchart is annotated, why certain actions in the code, etc. The demo will take place on the tagged VM and changes to the code during the demo will not be accepted. The code must be able to compile and run correctly, otherwise it will affect the feature score.SCM_RIGHTS
6. Other Recommendations
6.1 Backups
It is highly recommended to use Git to back up the job code regularly to prevent the loss of code due to file system corruption caused by kernel panics. It is also recommended to take a snapshot of the virtual machine before attempting the job programming part.
6.2 Related Man Pages
The following man pages may be useful for understanding existing features or job implementations:、、、、、、。CMSG_DATA(3)uvm(9)malloc(9)TAILQ_INIT(3)RB_PROTOTYPE(3)file(9)KASSERT(9)
6.3 Testing
Public tests: Public tests are provided, and it is recommended to run commands before running tests to reduce the possibility of file system corruption in the event of a kernel panic. Public tests only test basic functionality, passing public tests does not guarantee that the code is completely correct, and students are encouraged to write their own test cases.syncSCM_PAGES
Test program installation: Test programs and 22 public tests can be installed by running a series of commands in the virtual machine.a2test
a2test program uses: the program accepts command line arguments, the first argument is optional, or, the second argument is optional, or, and the third argument is the list of tests to run/view the information/dump output or.a2testpublicprivatecustomallruninfodumpall

COMP3301 2024 Assignment 2

CS Tutor | Computer Programming Tutorial | Code Help | Programming Help

Whats-App: +254-712-495-486

Email: antotech874@gmail.com

Non-intermediary, contact the programmer directly
