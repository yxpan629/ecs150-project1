# ecs150-project1
0.Phase-0
To copy the sshell.c file from /home/cs150jp/public/p1/sshell.c to our directory, 
we used the cp command. To make the Makefile, we made a regular file and followed the 
format in (https://www.cs.swarthmore.edu/~newhall/unixhelp/howto_makefiles.html)

1.Phase-1
To implement the fork+exec+wait method, we followed the example in the lecture powerpoints. 
We modified the fprintf statement to print with the wait exit status of the child process.

2.Phase-2
To read commands from the input, we first used the write() syste, call to prompt to stdout with 
'sshell$ '. To grab the input from the user, we used fgets() and put the input into a buffer array.
To handle errors, we first made an array containing all the error messages, but later defined global
variables with each error message because we were accessing the array with constants which made our
code less readable. For example instead of error[1] we used a global variable NO_DIRECTORY.

3.Phase-3
To implement arguments in our command execution, we parsed the input string using strtok(). 
We used a pointer array to store each parsed substring because execvp() does not take a char array 
type, but rather it takes pointers. Thus, we were able to tell the command from the arguments, as the 
command is the first element in the pointer array.

4.Phase-4
Each builtin command was defined in its own function. To implement the exit command, we used the 
write system call to output a custom message to stdout. In order to exit the shell, we checked if the 
input was the exit command first. If so, we returned the function call to the buitin command. In the 
exit buitin command, we returned EXIT_SUCCESS, which is what is returned in the main to exit an 
application. To implement the pwd command, we called getcwd(), stored the return value into a 
pointer, and printed the returned current working directory to stdout. To implement the cd command, 
we called chdir with the first argument following the command because the cd command is guaranteed to 
only be called with one argument. We also used a flag to check the return value of chdir() to check 
for an error in the directory that was inputed. If the return value was -1, it meant the directory 
did not exist so we were able to implement that error after this call.

5.Phase-5
To implement input redirection, we defined a struct object that contained the command, the arguments
to the command, the number of arguments, a bool for input redirection, a bool for output redirection,
and the file which was either the input or output file being redirected. We decided to define this
struct because we need the command separated from the arguments to use exec(), and we need flags for
redirection in order to know if redirection is present in the user input so that we can redirect
accordingly or continue execution as a regular command with no redirection. We also decided to have
the number of argument available to us because we need to put the command and arguments back together
into a pointer array to call exec(). To do this we used a for loop, for which we needed to know the
number of arguments in the command to create the approrpiate condition of the for loop. 
To implement input rediraction, we chose to have another parsing function, which first checked for an
input redirection symbol '<' using strstr(). If the redirection symbol was present, we set the
input redirection bool of the struct object to true. Next, we used the struct object's bool member
variables to check if we needed to do further parsing. If the redirection symbol was not present, we
called the regular parsing function. However, if the redirection symbol was present, first we used
strtok() to parse the string at the symbol and grab the file which should be anything immidiately
following the symbol. We can then call the regular parsing function with the left side of the
redirection symbol substring because this substring contains the actual command and arguments. The
regular parsing funnction then returns the struct object to the redirection parsing function, which
also returns the struct object back to main. In main we used the bool variables of the struct object
again to check if the input was a command with redirection. If so, we called another function to
perform the redirection. To redirect the input file, we used open() to open the file and obtain a 
file descriptor for the file. Then, we were able to used dub2() to close stdin and replace it with 
the file descriptor of the file opened. We also checked for errors at the beginning of this function 
by checking if the input had in fact a redirection symbol (using the bool variable), but the file 
variable was NULL. In which case, there was no input file.

6.Phase-6
To implemet output redirection, we followed the exact same procedure as the input redirection, which 
just consisted of adding an extra case to check for the output redirection symbol rather than the 
input redirection symbol. After parsing the input, just like calling the input redirection function 
if the input redirecgtion symbol was present, we called the output redirection function if the output 
redirection symbol was present. However, in the output redirection function, we closed stdout instead 
of stdin and replaced it with the file descriptor of the file using dup2(). In this phase we realized 
that we needed to connect stdout back to the file descriptor 1 so that we could print the completed 
message to the terminal and prompt again. To do this, we used a temporary variable to open stdout (or 
stdin for input redirection) so that we could store the stdout "file." After redirecting stdout to 
the file, we executed the command, and after used dup2() again to restore stdout in the temp variable 
back to file descriptor 1.

7.Phase-7
To implement pipeline commands, we first checked the input string using strstr() to check for the
pipeline symbol. If the symbol was not present, we continued checked with redirection. Otherwise, we 
called another parsing function for input with pipeines. We decided to create another struct object 
that contained an array of struct objects to store the commands, and another variable to store the 
number of commands. Then we were able to use strtok() to parse the input string at the pipelines,
saved it to an array, and then loop thorugh that array to parse each of those strings because each of 
those strings are processes. Thus, we called the redirection parsing on each of the elements of that
array and saved it on the array of the pipelinne struct object. Here we got stuck because the 
redirection parsing function the same address for each struct command object. This caused the
array containing the commands in the pipeline struct object to all have the same command. We were not 
able to figure out this issue, but we did try implementing a pipeline function to redirect input and 
output of each adjacent command. We copied the pipeline function from lecture and modified it to 
checkc for redirection commands and call our own execute function instead of execvp(), as our own 
execute function checked for missing commands and put the command and arguments back together into a 
pointer array needed to call execvp().

8.Phase-8
We spend so much time trying to figure out why phase 7 was not working that we ran out of time to implement phase 8.
