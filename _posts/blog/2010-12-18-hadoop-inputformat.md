---
layout: post
title:  "hadoop自定义InputFormat"
categories: blog
tags: Hadoop Java
---

接触hadoop一年多了，但是自己一直没有用hadoop写过什么程序。最近，由于项目需要，将一些文件转换成hadoop的MapFile。网上的例子基本是直接处理文本输入，自定义输入格式的见到两个，但是都是用的旧的API，用新API写的还没有，可能高手不屑于写这些。但是处理自定义输入是每个用hadoop的人都要学会才行的，因为不是每个人的输入都是文本文件。

<!--more-->

数据输入是hadoop的第一步，不能读自己的数据，后面的处理就无从谈起。文本格式处理起来容易些，对于二进制格式的文件，虽然hadoop有一个SequenceFileInputFormat，可以先把自己的数据转成SequenceFile，再处理，但是这样要多一倍的处理时间、存储空间。无奈之下，参考了hadoop的源代码，自己写了个ConverterInputFormat，在这里贴出来，供大家参考。

代码是基于hadoop 0.20的，其中的FetcherOutput是用Java的DataOutputStream写入到本地磁盘的，可以换成自己想要的格式。ConvertertRecordReader好像必须有个默认的构造器。

{% highlight java %}
package com.randyho.hadoop.converter;

import java.io.DataInputStream;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.InputSplit;
import org.apache.hadoop.mapreduce.JobContext;
import org.apache.hadoop.mapreduce.RecordReader;
import org.apache.hadoop.mapreduce.TaskAttemptContext;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.FileSplit;

import com.randyho.FetcherOutput;
    
public class ConverterInputFormat extends FileInputFormat<Text, FetcherOutput> {

    // Do not split files.
    protected boolean isSplitable(JobContext context, Path file) {
        return false;
    }

    public RecordReader<Text, FetcherOutput> createRecordReader(
            InputSplit split, TaskAttemptContext context) throws IOException,
            InterruptedException {
        return new ConvertertRecordReader();
    }

    class ConvertertRecordReader extends RecordReader<Text, FetcherOutput> {
       
        private DataInputStream dis;
        private Text key = null;
        private FetcherOutput value;
        private boolean more = true;
        private Configuration conf;

        public ConvertertRecordReader(){
            key = new Text();
            value = new FetcherOutput();
            more = true;
        }
       
        public void close() throws IOException {
            if (dis != null) {
                dis.close();
            }
        }

        public Text getCurrentKey() throws IOException, InterruptedException {
            return key;
        }

        public FetcherOutput getCurrentValue() throws IOException,
                InterruptedException {
            return value;
        }

        public float getProgress() throws IOException, InterruptedException {
            return more ? 0f : 100f;
        }

        public void initialize(InputSplit gensplit, TaskAttemptContext context)
                throws IOException, InterruptedException {
            FileSplit split = (FileSplit) gensplit;
            conf = context.getConfiguration();  
            Path file = split.getPath();
            FileSystem fs = file.getFileSystem(conf);
           
            System.out.println(&quot;reading: &quot; + file);

            // open the file
            dis = fs.open(split.getPath());
        }

        public boolean nextKeyValue() throws IOException, InterruptedException {
            if (dis.available() != 0) {
                value.readFields(dis);
                key.set(value.getUrl());                
                return true;
            } else {
                more = false;
                return false;
            }
        }
    }
}
{% endhighlight %}

本人也是新学，对hadoop也不是很熟悉，如果有更好的方式，恳请赐教。
