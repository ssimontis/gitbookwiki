# TTY Devices

* Emulation of a teletype device which formerly consisted of
  * Line discipline mode \(raw or canonical\)
  * UART settings
  * Session management \(TTY driver\)
* User process affects TTY behavior by writing to file under /dev
  * Logged in user becomes device owner
  * Achieved with login program
* UART has been replaced with video terminal
  * Frame buffer of characters and graphical character attributes
  * Terminal emulation pushed into userland
  * Pseudo Terminal \(pty\) handles userland emulation
* Linux Process States
  * Running or runnable \(on run queue\)
  * Uninterruptible sleep \(waiting for event\)
  * Interruptible sleep \(waiting on event or signal\)
  * Stopped by job control signal or debugger trace
  * Zombie process, terminated but not yet reaped by parent
* Jobs are process groups
  * Jobs are manipulated in context of session
  * Session managed by session leader, the shell, which communicates with kernel using signals and system calls

