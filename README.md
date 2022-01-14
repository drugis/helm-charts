# Helm charts for Drugis

## Troubleshooting

### Recover a persistent volume
When you delete an application and delete the namespace the volume is still recoverable. 
> This will only work when a `nfs-provisioner-retain` *StorageClass* is chosen.

1. Navigate to the cluster level 
2. Click "Storage" and then "Persistent Volumes"
3. Search for the name of the application e.g. "gemtc" 
4. Copy the volume **Name**
5. On the cluster level you can fire up a terminal using `Launch kubectl`
6. Execute `kubectl patch pv #volume-name# -p '{"spec":{"claimRef": null}}'`
   > Use the copied *volume name*, not *volume claim name*
7. Navigate to the project you want to deploy into
8. Navigate to "Resources" then "Workloads" then "Volumes" 
9. Click on "Add Volume", give it a *Name*, select the correct *Namespace* and select *Use existigin persistent volume* 
10. Search for the *volume name* and select it
11. Then redeploy the instance with an *existing volume claim* pointing to the one you just created.
#### Database recovery
**IMPORTANT: be sure you save the database password!** You will need it when you redeploy the instance.
Copy the backup dump to the container.

`rancher kubectl cp ~/backups/2022-01-13_test_jena_es_backup.n4.gz addis-jena-es:/tmp/. -n addis`

Shell into the pod

`rancher kubectl exec -it pod/addis-jena-es bash -n addis`

Drop the existing database

`dropdb database -U rootUser`

Fill password for postgres root-user

Create the database again

`createdb database -U applicationUser`

Fill in password for application user and load the dump.

`psql database -U applicationUser < /tmp/database-dump.sql`

#### Jena recovery
**Apache Jena 2.13 is needed**
Copy the backup dump to the container.

`rancher kubectl cp ~/backups/2022-01-13_test_jena_es_backup.n4.gz addis-jena-es:/tmp/. -n addis`

Shell into the pod

`rancher kubectl exec -it pod/addis-jena-es bash -n addis`

Get Apache Jena

`wget http://archive.apache.org/dist/jena/binaries/apache-jena-2.13.0.zip`

Unzip the archive

`unzip apache-jena-2.13.0.zip`

Remove the lock and cd to `/`

`rm /DB/tdb.lock`
`cd  /`

To recover Jene you need the tdbloader command

`/#jena-path#/bin/tdbloader --loc=DB /#backup-path#/$(date +%Y-%m-%d)_test_jena_es_backup.n4.gz`

The --loc=DB is sub-directory location where you at.

### Add docker registry secret
You can add a a registry to each project to be able to use the `imagePullSecret` value in the chart. This is needed for the Enterprise edition of the Addis platform.
1. Click on "Resources"
2. Click on "Secrets"
3. Click on "Registry secrets"
4. Select "Custom"
5. Fill in "https://registry.molgenis.org/repository/helm"
6. Fill in "Username" and "Password"
