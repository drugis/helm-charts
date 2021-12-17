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