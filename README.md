# Apache Toree Quickstart

## Install

Install Jupyter and Toree.

    sudo pip install --upgrade pip
    sudo pip install jupyter
    sudo pip install toree

## Configure

Define `SPARK_HOME`.

    SPARK_HOME=$HOME/Downloads/spark-1.6.0-bin-hadoop2.6

Configure Toree. 

    jupyter toree install \
      --spark_home=$SPARK_HOME

## Start

Start notebook.

    jupyter notebook

Point browser to <http://localhost:8888>.

Then open a new notebook using *New > Toree*.

## Test

Test notebook with simple Spark Scala code.

```scala
sc.parallelize(1 to 100).
  filter(x => x % 2 == 0).
  map(x => x * x).
  take(3)
```

Use tab for auto-complete.

# 3rd Party Libraries

## Configure

List all packages you will use.

    SPARK_PKGS=$(cat << END | xargs echo | sed 's/ /,/g'
    com.databricks:spark-csv_2.10:1.4.0
    com.databricks:spark-avro_2.10:2.0.1
    END)

Define `SPARK_OPTS` and `SPARK_HOME`.

    SPARK_OPTS="--packages=$SPARK_PKGS"
    SPARK_HOME=$HOME/Downloads/spark-1.6.0-bin-hadoop2.6

Configure Toree to use these packages.

    jupyter toree install \
      --spark_home=$SPARK_HOME \
      --spark_opts=$SPARK_OPTS

## Start

Start notebook.

    jupyter notebook

Point browser to <http://localhost:8888>.

Then open a new notebook using *New > Toree*.

## Troubleshooting

If you run into issues downloading dependencies wipe out `~/.m2` and
`~/.ivy2`. Spark uses Ivy2 which sometimes corrupts these folders.
This is a likely source of dependency errors.

## Test CSV library

```scala
// Grab URL contents
def getUrl(url:String):String = 
  scala.io.Source.fromURL(url).mkString

// Write file
def fileWrite(path:String,contents:String) = {
  import java.io.{PrintWriter,File}
  val writer = new PrintWriter(new File(path))
  writer.write(contents)
  writer.close
}

val symbol = "AAPL"
val baseUrl = "http://real-chart.finance.yahoo.com"
val url = s"${baseUrl}/table.csv?s=${symbol}&g=d&ignore=.csv"
val csv = getUrl(url)
val csvFile = s"${symbol}.csv"
fileWrite(csvFile, csv)

val stockRdd = sc.textFile(csvFile).
  filter(line => line matches ".*\\d.*").
  map(line => line.split(",")).
  map(fields => (fields(6).toDouble,fields(0))).
  sortBy({case (close,date) => close},false)

stockRdd.take(5).foreach(println)

import org.apache.spark.sql.SQLContext

// Load table.
val df = sqlContext.read.
    format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("AAPL.csv")

// Explore the table.
df.show
df.registerTempTable("aapl")
sqlContext.sql("SELECT COUNT(1) FROM aapl").show
sqlContext.sql("SELECT MAX(`Adj Close`) FROM aapl").show
sqlContext.sql("SELECT Date,`Adj Close` FROM aapl 
  ORDER BY `Adj Close` DESC LIMIT 5").show
```

# Misc

## Publishing GitHub Notebooks

How can I share my notebook with other people?

- Go to <https://gist.github.com>
- Create gist with extension `.ipynb`
- Paste your notebook here.
- Copy the Gist ID.
- Go to <http://nbviewer.jupyter.org>
- Paste the Gist ID here.
- Share the link NBViewer gives you.

## Creating Slide Shows

How can I create a slide show from a notebook?

- Create a Toree notebook
- Click on *View > Cell Toolbar > Slideshow*
- `jupyter nbconvert notebook.ipynb --to slides --post serve`
- Open browser at <http://localhost:8000>





