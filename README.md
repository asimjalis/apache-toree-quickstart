# Apache Toree Quickstart

## Presentation

Presentation accompanying this tutorial:

<http://www.slideshare.net/asimjalis/apache-toree>

## Preamble

All of the following commands should be executed in a new terminal
window. You do not need to modify your `.profile` or `.bashrc` or any
other configuration file.

## Install Spark

Install Spark from <http://spark.apache.org/downloads.html>.

Make sure you download the pre-built *binaries* for Hadoop 2.6 and
later, and not the sources. (By default the download link will point
to the sources and not the binaries.)

## Install Pip, Jupyter, Toree

Install pip if you don't already have it installed.

    sudo easy_install pip

If you already have pip then make sure it is updated.

    sudo pip install --upgrade pip

Install Jupyter and Toree.

    sudo pip install jupyter
    sudo pip install toree

## Configure

Set `SPARK_HOME` to point to the directory where you downloaded and
expanded the Spark binaries. Instead of 1.6.1 you might have a
different version number.

    SPARK_HOME=$HOME/Downloads/spark-1.6.1-bin-hadoop2.6

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
  take(10)
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

Now go to the notebook [Demo.ipynb](Demo.ipynb) and test code there.

# Going Public

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
- `jupyter nbconvert NOTEBOOK.ipynb --to slides --post serve`
- Open browser at <http://localhost:8000>

## Slide Show Demo

To view the slides demo:

    jupyter nbconvert Slides-Demo.ipynb --to slides --post serve

Open browser at <http://localhost:8000>.
