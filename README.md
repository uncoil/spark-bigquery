spark-bigquery
==============

Google BigQuery support for Spark, SQL, and DataFrames.

| spark-bigquery version | Spark version | Comment |
| :--------------------: | ------------- | ------- |
| 0.2.x | 2.x.y | Active development |

To use the package in a Google [Cloud Dataproc](https://cloud.google.com/dataproc/) cluster:

`spark-shell --packages com.shina:spark-bigquery:0.2.3-SNAPSHOT`

To use it in a local SBT console:

```scala
import com.shina.spark.bigquery._

// Set up GCP credentials
sqlContext.setGcpJsonKeyFile("<JSON_KEY_FILE>")

// Set up BigQuery project and bucket
sqlContext.setBigQueryProjectId("<BILLING_PROJECT>")
sqlContext.setBigQueryGcsBucket("<GCS_BUCKET>")

// Set up BigQuery dataset location, default is US
sqlContext.setBigQueryDatasetLocation("<DATASET_LOCATION>")
```

Usage:

```scala
// Load everything from a table
val table = sqlContext.bigQueryTable("bigquery-public-data:samples.shakespeare")

// Load results from a SQL query
// Only legacy SQL dialect is supported for now
val df = sqlContext.bigQuerySelect(
  "SELECT word, word_count FROM [bigquery-public-data:samples.shakespeare]")

  // Save data to a table
df.saveAsBigQueryTable("my-project:my_dataset.my_table")
```

# Why this repo?



# Publishing to Maven Central

The proceeding steps do not follow in any particular order but are all required. You can check `build.sbt` for example.

Step 1: Add a distributed management section to your `build.sbt`. For example:

```sbt
// Remove all additional repository other than Maven Central from POM
pomIncludeRepository := { _ => false }
publishTo := {
  val nexus = "https://oss.sonatype.org/"
  if (isSnapshot.value) Some("snapshots" at nexus + "content/repositories/snapshots")
  else Some("releases" at nexus + "service/local/staging/deployByRepositoryId/iogithubodidere-1002")
}
publishMavenStyle := true
```

Step 2: Add the SCM section.

```sbt
scmInfo := Some(
  ScmInfo(
    url("https://github.com/odidere/spark-bigquery"),
    "scm:git@github.com/odidere/spark-bigquery.git"
  )
)
```

Step 3: [Install GNU PG](https://www.gnupg.org/download/). 

Step 4: Generate PGP key pair.

```bash
$gpg --gen-key
```

List the keys:

```bash
$gpg --list-keys

/home/foo/.gnupg/pubring.gpg
------------------------------

pub   rsa4096 2018-08-22 [SC]
      1234517530FB96F147C6A146A326F592D39AAAAA
uid           [ultimate] your name <you@example.com>
sub   rsa4096 2018-08-22 [E]
```

Distribute the key:
```bash
$gpg --keyserver hkp://pool.sks-keyservers.net --send-keys 1234517530FB96F147C6A146A326F592D39A
```

Step 5: With the PGP key you want to use, you can sign the artifacts you want to publish to the Sonatype repository with the sbt-pgp plugin.

Add the following line to your `~/.sbt/1.0/plugins/gpg.sbt` file to enable it globally for SBT projects:

```sbt
addSbtPlugin("com.jsuereth" % "sbt-pgp" % "1.1.1")
```

Note: The plugin is a solution to sign artifacts. It works with the GPG command line tool.

Make sure that the gpg command is in PATH available to the sbt, add `useGpg :=` true to your build.sbt to make the plugin gpg-aware.

Step 6: [Sign up for a Sonatype Jira account](https://issues.sonatype.org/secure/Signup!default.jspa).

Step 7: The credentials for your Sonatype OSSRH account need to be stored somewhere safe (e.g. NOT in the repository). Common convention is a $HOME/.sbt/1.0/sonatype.sbt file, with the following:

```sbt
credentials += Credentials(Path.userHome / ".sbt" / "sonatype_credential")
```

Next create a file `~/.sbt/sonatype_credential`:
```sbt
realm=Sonatype Nexus Repository Manager
host=oss.sonatype.org
user=<your username>
password=<your password>
```

Note: The first two strings must be "Sonatype Nexus Repository Manager" and "oss.sonatype.org".

Step 8: [Create a Jira](https://issues.sonatype.org/secure/CreateIssue.jspa?issuetype=21&pid=10134) issue for new project hosting.

Step 10: From sbt shell run:
```bash
$sbt
>publishSigned
```

You can find more details on https://central.sonatype.org/pages/ossrh-guide.html.

# Further work

I was originally asked to use the package `io.github.odidere` by maven central admin, a reversal of my github address. This should normally be `com.uncoil` or `ai.unicoil`, the reverse of Uncoil.ai. Note that the company must own any domain that is chosen.

# License

Derived from works - Copyright 2016 Spotify AB.
Licensed under the Apache License, Version 2.0: http://www.apache.org/licenses/LICENSE-2.0
