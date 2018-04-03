# Shell

Shell implements an idiomatic Ruby interface for common UNIX shell commands.

It provides users the ability to execute commands with filters and pipes, like
`sh`/`csh` by using native facilities of Ruby.

## Examples

### Temp file creation

In this example we will create three `tmpFile`'s in three different folders
under the `/tmp` directory.

    sh = Shell.cd("/tmp") # Change to the /tmp directory
    sh.mkdir "shell-test-1" unless sh.exists?("shell-test-1")
    # make the 'shell-test-1' directory if it doesn't already exist
    sh.cd("shell-test-1") # Change to the /tmp/shell-test-1 directory
    for dir in ["dir1", "dir3", "dir5"]
      if !sh.exists?(dir)
        sh.mkdir dir # make dir if it doesn't already exist
        sh.cd(dir) do
          # change to the `dir` directory
          f = sh.open("tmpFile", "w") # open a new file in write mode
          f.print "TEST\n"            # write to the file
          f.close                     # close the file handler
        end
        print sh.pwd                  # output the process working directory
      end
    end

### Temp file creation with self

This example is identical to the first, except we're using
Command`Processor#transact`.

Command`Processor#transact` executes the given block against self, in this case
`sh`; our Shell object. Within the block we can substitute `sh.cd` to `cd`,
because the scope within the block uses `sh` already.

    sh = Shell.cd("/tmp")
    sh.transact do
      mkdir "shell-test-1" unless exists?("shell-test-1")
      cd("shell-test-1")
      for dir in ["dir1", "dir3", "dir5"]
        if !exists?(dir)
          mkdir dir
          cd(dir) do
            f = open("tmpFile", "w")
            f.print "TEST\n"
            f.close
          end
          print pwd
        end
      end
    end

### Pipe /etc/printcap into a file

In this example we will read the operating system file `/etc/printcap`,
generated by `cupsd`, and then output it to a new file relative to the `pwd`
of `sh`.

    sh = Shell.new
    sh.cat("/etc/printcap") | sh.tee("tee1") > "tee2"
    (sh.cat < "/etc/printcap") | sh.tee("tee11") > "tee12"
    sh.cat("/etc/printcap") | sh.tee("tee1") >> "tee2"
    (sh.cat < "/etc/printcap") | sh.tee("tee11") >> "tee12"

[Shell Reference](https://ruby-doc.org/stdlib-2.5.0/libdoc/shell/rdoc/Shell.html)