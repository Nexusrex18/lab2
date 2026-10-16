---

## Program 3: Command Injection (DVWA on Metasploitable)

1. Download Metasploitable:
   `https://sourceforge.net/projects/metasploitable/` → get `Metasploitable2-Linux.zip`

2. Extract the ZIP → should contain `Metasploitable.vmdk` and `Metasploitable.vmx`

3. In VirtualBox: **New** → Name: `Metasploitable`
   - Folder: default, ISO: none
   - Type: Linux, Distribution: Linux 2.6, Version: Ubuntu (32-bit) or Other Linux (32-bit)
   - Under **Specify Virtual Hard Disk** → **Use an Existing Virtual Hard Disk File** → select `Metasploitable.vmdk` → **Finish**

4. Set networking:
   - Metasploitable → Settings → Network → Adapter 1 → **Host-only Adapter** (or Bridged)
   - Kali → Settings → Network → Adapter 1 → same type as Metasploitable

5. Start Metasploitable → login:
   ```
   Username: msfadmin
   Password: msfadmin
   ```

6. Run:
   ```bash
   ifconfig
   ```
   Note the IP under `eth0` (e.g., `192.168.56.102`)

7. Start Kali → open terminal → test connection:
   ```bash
   ping 192.168.56.102
   ```

8. Open Firefox in Kali → go to:
   ```
   http://192.168.56.102/dvwa
   ```

9. Login:
   ```
   Username: admin
   Password: password
   ```
   (Click **Create/Reset Database** first if prompted, then log in again)

10. Left menu → **DVWA Security** → select **Low** → **Submit**

11. Left menu → **Command Execution**

12. Enter in the IP field, click **Submit** each time:
    ```
    127.0.0.1
    127.0.0.1; ls
    127.0.0.1; pwd
    127.0.0.1; whoami
    127.0.0.1; ls -la
    127.0.0.1; ifconfig
    ```

------------------------------------------------------------------------------------------------------------------------------------

## Program 4: Buffer Overflow using GCC

1. Open terminal in Kali Linux.

2. Create the program file:
   ```bash
   nano buffer.c
   ```

3. Paste this code:
   ```c
   #include <stdio.h>
   #include <string.h>

   int main()
   {
       char buff[15];
       int pass = 0;

       printf("\nEnter the password:\n");

       fgets(buff, sizeof(buff), stdin);
       buff[strcspn(buff, "\n")] = '\0';   // Remove newline

       if(strcmp(buff, "thegeekstuff"))
       {
           printf("\nWrong Password\n");
       }
       else
       {
           printf("\nCorrect Password\n");
           pass = 1;
       }

       if(pass)
       {
           printf("\nRoot privileges given to the user\n");
       }

       return 0;
   }
   ```

4. Save and exit: `Ctrl + O` → `Enter` → `Ctrl + X`

5. Compile:
   ```bash
   gcc -g -fno-stack-protector -z execstack -o buffer buffer.c
   ```

6. Run and test (do each separately):
   ```bash
   ./buffer
   ```
   - Input: `thegeekstuff` → observe output
   - Run again, input: `hello` → observe output
   - Run again, input a long string (more than 15 characters), e.g.:
     ```
     hhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhh
     ```
   - Observe crash / unexpected behavior

---------------------------------------------------------------------------------------------------------------------

## Program 5: File Comparison using Compare It!

1. Open **Notepad**, type:
   ```
   Student Name: Rahul
   USN: 1DS22CS001
   Department: CSE
   Semester: 7
   College: DSCE
   ```
   Save as `File1.txt` (e.g., Desktop)

2. Open **Notepad** again, type:
   ```
   Student Name: Rahul Kumar
   USN: 1DS22CS001
   Department: Computer Science
   Semester: 7
   College: DSCE Bangalore
   ```
   Save as `File2.txt` in the same folder as `File1.txt`

3. Download Compare It!:
   `https://www.grigsoft.com/wincmp3.htm`
   Run installer → follow wizard → **Finish**

4. Open **Compare It!** from Start Menu / Desktop shortcut.

5. Left panel → **Browse/Open** → select `File1.txt` → **Open**

6. Right panel → **Browse/Open** → select `File2.txt` → **Open**

7. Files compare automatically and display side by side with differences highlighted (Yellow = modified, Green = added, Red = deleted).

8. Use toolbar/keyboard shortcuts to move to **next**/**previous** difference.

9. (Optional) Select a difference → **Copy Left → Right** or **Copy Right → Left** to sync.

10. Generate report: **File → Print** or **Print Preview** → save/print for documentation.

11. Close the application.