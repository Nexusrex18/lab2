# Lab 2 — HDFS File Operations with Java FileSystem API

## 1. Create Lab Folder

```powershell
cd C:\bda-labs
mkdir lab2-hdfs
cd lab2-hdfs
```

## 2. Create Docker Compose File

```powershell
notepad docker-compose.yml
```

Paste:

```yaml
services:
  namenode:
    image: apache/hadoop:3.5.0
    hostname: namenode
    command: ["hdfs", "namenode"]
    ports:
      - "9870:9870"
    env_file: ./config
    environment:
      ENSURE_NAMENODE_DIR: "/tmp/hadoop-root/dfs/name"

  datanode:
    image: apache/hadoop:3.5.0
    command: ["hdfs", "datanode"]
    env_file: ./config
```

Save and close Notepad.

## 3. Create Hadoop Configuration

```powershell
notepad config
```

Paste:

```text
CORE-SITE.XML_fs.default.name=hdfs://namenode
CORE-SITE.XML_fs.defaultFS=hdfs://namenode
HDFS-SITE.XML_dfs.namenode.rpc-address=namenode:8020
HDFS-SITE.XML_dfs.replication=1
```

Save and close Notepad.

## 4. Start HDFS

```powershell
docker compose up -d
```

Wait approximately 15 seconds, then:

```powershell
docker compose exec namenode bash
```

## 5. Create HDFS Directory

```bash
hdfs dfs -mkdir -p /user/student
hdfs dfs -ls /user
```

## 6. Create Java Program

```bash
nano HdfsDemo.java
```

Paste:

```java
import java.net.URI;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileStatus;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.FSDataInputStream;
import org.apache.hadoop.fs.FSDataOutputStream;
import org.apache.hadoop.fs.Path;

public class HdfsDemo {

    public static void main(String[] args) throws Exception {

        String uri = "hdfs://namenode:8020";

        Configuration conf = new Configuration();

        FileSystem fs = FileSystem.get(
                URI.create(uri),
                conf
        );

        Path file = new Path(
                "/user/student/quangle.txt"
        );

        // WRITE
        FSDataOutputStream out = fs.create(file);

        out.writeBytes(
                "On the top of the Crumpetty Tree\n"
        );

        out.writeBytes(
                "The Quangle Wangle sat\n"
        );

        out.close();

        System.out.println(
                "Wrote file: " + file
        );

        // READ + SEEK
        FSDataInputStream in = fs.open(file);

        byte[] buffer = new byte[32];

        in.read(buffer);

        System.out.println(
                "From start : " +
                new String(buffer).trim()
        );

        in.seek(33);

        byte[] buf2 = new byte[32];

        in.read(buf2);

        System.out.println(
                "After seek : " +
                new String(buf2).trim()
        );

        in.close();

        // METADATA
        FileStatus st = fs.getFileStatus(file);

        System.out.println(
                "Length : " +
                st.getLen() +
                " bytes"
        );

        System.out.println(
                "Replication: " +
                st.getReplication()
        );

        // LIST
        System.out.println(
                "--- listStatus /user/student ---"
        );

        for (FileStatus s :
                fs.listStatus(
                        new Path("/user/student"))) {

            System.out.println(
                    " " + s.getPath().getName()
            );
        }

        // GLOB
        System.out.println(
                "--- globStatus *.txt ---"
        );

        for (FileStatus s :
                fs.globStatus(
                        new Path("/user/student/*.txt"))) {

            System.out.println(
                    " " + s.getPath().getName()
            );
        }

        fs.close();
    }
}
```

Save and exit:

```text
Ctrl+O
Enter
Ctrl+X
```

## 7. Compile and Package

```bash
export HADOOP_CLASSPATH=$(hadoop classpath)

mkdir classes

javac -classpath $HADOOP_CLASSPATH -d classes HdfsDemo.java

jar -cvf hdfsdemo.jar -C classes .
```

## 8. Run Java Program

```bash
hadoop jar hdfsdemo.jar HdfsDemo
```

Expected:

```text
Wrote file: /user/student/quangle.txt
From start : On the top of the Crumpetty Tree
After seek : The Quangle Wangle sat
Length : 56 bytes
Replication: 1
--- listStatus /user/student ---
 quangle.txt
--- globStatus *.txt ---
 quangle.txt
```

## 9. Verify File on HDFS

```bash
hdfs dfs -cat /user/student/quangle.txt
```

## 10. Add Second File

```bash
echo "hello hdfs" | hdfs dfs -put - /user/student/notes.txt
```

## 11. Run Program Again

```bash
hadoop jar hdfsdemo.jar HdfsDemo
```

## 12. Check HDFS Files

```bash
hdfs dfs -ls /user/student
```

## 13. Cleanup

```bash
exit
```

```powershell
docker compose down
```