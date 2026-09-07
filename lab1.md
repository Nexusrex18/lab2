# Lab 1 — Word Count in MapReduce

## 1. Start Hadoop Container

```powershell
cd C:\bda-labs
mkdir lab1-wordcount
cd lab1-wordcount

docker run -dit --name hadoop apache/hadoop:3.5.0 bash
docker exec -it hadoop bash
```

## 2. Create Java Program

```bash
nano WordCount.java
```

Paste:

```java
import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCount {

    // MAPPER: (offset, line) -> (word, 1)
    public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable> {

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(
                Object key,
                Text value,
                Context context)
                throws IOException, InterruptedException {

            StringTokenizer itr =
                    new StringTokenizer(value.toString());

            while (itr.hasMoreTokens()) {
                word.set(itr.nextToken().toLowerCase());
                context.write(word, one);
            }
        }
    }

    // REDUCER: (word, [1,1,1,...]) -> (word, sum)
    public static class IntSumReducer
            extends Reducer<Text, IntWritable, Text, IntWritable> {

        private IntWritable result = new IntWritable();

        public void reduce(
                Text key,
                Iterable<IntWritable> values,
                Context context)
                throws IOException, InterruptedException {

            int sum = 0;

            for (IntWritable val : values) {
                sum += val.get();
            }

            result.set(sum);
            context.write(key, result);
        }
    }

    // DRIVER
    public static void main(String[] args) throws Exception {

        Configuration conf = new Configuration();

        Job job = Job.getInstance(conf, "word count");

        job.setJarByClass(WordCount.class);

        job.setMapperClass(TokenizerMapper.class);

        job.setCombinerClass(IntSumReducer.class);

        job.setReducerClass(IntSumReducer.class);

        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        FileInputFormat.addInputPath(
                job,
                new Path(args[0]));

        FileOutputFormat.setOutputPath(
                job,
                new Path(args[1]));

        System.exit(
                job.waitForCompletion(true) ? 0 : 1
        );
    }
}
```

Save and exit:

```text
Ctrl+O
Enter
Ctrl+X
```

## 3. Create Input Files

```bash
mkdir input

echo "the quick brown fox the lazy dog the fox" > input/file1.txt
echo "a quick dog and a brown fox jumped" > input/file2.txt
```

## 4. Compile

```bash
export HADOOP_CLASSPATH=$(hadoop classpath)

mkdir classes

javac -classpath $HADOOP_CLASSPATH -d classes WordCount.java

jar -cvf wordcount.jar -C classes .
```

## 5. Run MapReduce Job

```bash
hadoop jar wordcount.jar WordCount input output
```

## 6. View Output

```bash
cat output/part-r-00000
```

Expected:

```text
a 2
and 1
brown 2
dog 2
fox 3
jumped 1
lazy 1
quick 2
the 3
```

## 7. Run Again Without Combiner

Remove the output directory:

```bash
rm -r output
```

Edit the Java file:

```bash
nano WordCount.java
```

Comment out:

```java
// job.setCombinerClass(IntSumReducer.class);
```

Recompile:

```bash
rm -rf classes
mkdir classes

javac -classpath $HADOOP_CLASSPATH -d classes WordCount.java

jar -cvf wordcount.jar -C classes .
```

Run again:

```bash
hadoop jar wordcount.jar WordCount input output
```

Check the output:

```bash
cat output/part-r-00000
```

Check the logs for:

```text
Combine input records=0
```

## 8. Cleanup

```bash
exit
```

```powershell
docker rm -f hadoop
```
