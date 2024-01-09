# Open Metadata

This project contains kustomization for deploying the Open Metadata project Helm charts on an OpenShift 4.x Cluster.

## What is Open Metadata?

Open Metadata is a unified platform for discovery, observability, and governance powered by a central metadata repository, in-depth lineage, and seamless team collaboration. It is one of the fastest-growing open-source projects with a vibrant community and adoption by a diverse set of companies in a variety of industry verticals. Based on Open Metadata Standards and APIs, supporting connectors to a wide range of data services, OpenMetadata enables end-to-end metadata management, giving you the freedom to unlock the value of your data assets.

Project Page: https://open-metadata.org/
Source Code: https://github.com/open-metadata/OpenMetadata

## Installing Open Metadata

Before starting the installation, logon on to the cluster:

```sh
$ oc logon ...
```

### EFS (NFS) Storage Class Requirements

Some of the sub-components of Open Metadata have a requirement for a storage class that supports ["ReadOnlyMany" or "ReadWriteMany"](https://github.com/airflow-helm/charts/blob/main/charts/airflow/values.yaml#L1355) otherwise airflow pods will fail to start. 

The simplest way to support this requirement on an Amazon AWS hosted cluster is to install the EFS storage driver. Additional details about how to install this can be found here: [EFS.md](EFS.md).

Before running the installation, ensure that the EFS storage class is available:

```sh
$ oc get storageclass
NAME                PROVISIONER       RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
efs                 efs.csi.aws.com   Delete          Immediate              false                  30d
```

### Running the Installation

The installation uses Kustomize to create the installation configuration from the Helm charts hosted by Open Metadata, and then patches in specific OpenShift configuration.

To perform the installation, run:

```sh
$ kustomize build components/apps/open-metadata/overlays --enable-helm | oc apply -f -
```

### Accessing the UI

The Open Metadata URL for accessing the user interface can be obtained from the route:

```sh
$ echo "\nOpen Metadata UI: https://$(oc get route open-metadata -o jsonpath='{.spec.host}' -n open-metadata)"
```