## Hadoop and Map Reduce

### components

1. Demo word count map reduce - 50xp
2. Demo city population - 50xp
3. Demo largest city in each state - 50xp

## Download and install Hadoop and Map Reduce
I used a virtual machine the included all the necessary components. The one I used was  [Cloudera Quickstart](https://www.cloudera.com/downloads/quickstart_vms/5-13.html)


## Word Count Example

### mapper.py
    #!/usr/bin/env python
    """mapper.py"""

    import sys

    # input comes from STDIN (standard input)
    for line in sys.stdin:
        # remove leading and trailing whitespace
        line = line.strip()
        # split the line into words
        words = line.split()
        # increase counters
        for word in words:
            # write the results to STDOUT (standard output);
            # what we output here will be the input for the
            # Reduce step, i.e. the input for reducer.py
            #
            # tab-delimited; the trivial word count is 1
            print ('%s\t%s' % (word, 1))#!/usr/bin/env python

### reducer.py

    #!/usr/bin/env python
    """reducer.py"""

    from operator import itemgetter
    import sys

    current_word = None
    current_count = 0
    word = None

    # input comes from STDIN
    for line in sys.stdin:
        # remove leading and trailing whitespace
        line = line.strip()

        # parse the input we got from mapper.py
        word, count = line.split('\t', 1)

        # convert count (currently a string) to int
        try:
            count = int(count)
        except ValueError:
            # count was not a number, so silently
            # ignore/discard this line
            continue

        # this IF-switch only works because Hadoop sorts map output
        # by key (here: word) before it is passed to the reducer
        if current_word == word:
            current_count += count
        else:
            if current_word:
                # write result to STDOUT
                print '%s\t%s' % (current_word, current_count)
            current_count = count
            current_word = word

    # do not forget to output the last word if needed!
    if current_word == word:
        print '%s\t%s' % (current_word, current_count)


### Testing outside of Hadoop

    cat inputText.txt | ./mapper | sort | ./reducer

### within Hadoop

#### 1. Put the file into the Hadoop cluster

    hadoop fs -put localfilename hadoopfilename

#### 3. Execute the mapreduce code


    hadoop jar /usr/lib/hadoop-0.20-mapreduce/contrib/streaming/hadoop-streaming-2.6.0-mr1-cdh5.13.0.jar  -file /home/cloudera/Documents/mapper.py -mapper /home/cloudera/Documents/mapper.py -file /home/cloudera/Documents/reducer.py -reducer /home/cloudera/Documents/reducer.py -input book -output bookout


(replace the paths with ones appropriate to your environment)

#### 4. look at the results

Some useful commands

    hadoop fs -ls 
    hadoop fs -ls bookout
     
    hadoop fs -text bookout/part-00000

