# Running EMR cluster and spark notebook


1. Create EMR Cluster
    - Sign in to the AWS Management Console and open the Amazon EMR console at https://console.aws.amazon.com/elasticmapreduce/.

    - Choose Create cluster to open the Quick Options wizard.

    - On the Create Cluster - Quick Options page, note the default values for Release, Instance type, Number of instances, and Permissions. These fields autopopulate with values chosen for general-purpose clusters. For more information about the Quick Options configuration settings, see Summary of Quick Options.

    - Change the following fields: Enter a Cluster name to help you identify the cluster. For example, My First EMR Cluster.

    - Leave Logging enabled, but replace the S3 folder value with the Amazon S3 bucket you created, followed by /logs. For example, s3://DOC-EXAMPLE-BUCKET/logs. This will create a new folder called 'logs' in your bucket, where EMR will copy the log files of your cluster.

    - Under Applications, choose the Spark option. Quick Options lets you select from the most common application combinations to install on your cluster.

    - Under Security and access, choose the EC2 key pair that you designated or created in Create an Amazon EC2 Key Pair for SSH.

    - Choose Create cluster to launch the cluster and open the cluster status page.

    - On the cluster status page, find the Status next to the cluster name. The status should change from Starting to Running to Waiting during the cluster creation process. You may need to choose the refresh icon on the right or refresh your browser to receive updates.

    - When the status progresses to Waiting, your cluster is up, running, and ready to accept work.

2. Run a jupyter notebook 