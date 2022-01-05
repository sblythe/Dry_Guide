# Checking your setup with a Test Script

To test that everything is setup properly, I have made a "test script" that can be edited appropriately according to the comments and then submitted to the batch scheduler to test that everything is running well in your local environment.

The basic test script is located at

```
/projects/p31603/Quest_Setup_Test_Script_PE.sh
```

To test your setup:

1) If you haven't done so, create a directory with your name at `projects/p31603/`

2) Make within your personal folder a directory for the test run (e.g., `TestSetup`)

3) Within your test-run directory, make a directory for the output of the test (e.g., `TestOutput`)

4) Now, copy the test script from the location above to your `TestSetup` directory. Use the `cp` command. 

5) Edit the **copy** of the script as indicated in the comments so that the filepaths are accurately rendered. It will not run without some edits from you.

6) The script on its own is also not yet executable. To make it executable, run the command (while in the directory that contains your copy of the test script):

```
chmod u+x Quest_Setup_Test_Script_PE.sh
```

This makes you (the **u**ser) able to e**x**ecute the file.

7) To run the script using the batch scheduler, run (from within the directory containing the newly executable copy of the script):

```
sbatch Quest_Setup_Test_Script_PE.sh
```

8) On an early test, it took about seven minutes for the test to run. You can check the status of the run using `sacct -X`. When it is done, the output of `sacct` will say "COMPLETED". To check how many resources the test used, you can run `seff <jobID>`, where the Job ID is the first column of the output from `sacct`. This is useful to know so that you can scale your demands for resources accordingly, particularly the memory, time, and number of cores. 