# Appendix

This appendix provides an overview of the process of migrating from the old Security Onion 2.4 to the new Security Onion 3.0.

!!! TIP
    
    If you are a current Security Onion Solutions customer with Professional Services or Appliance coverage, contact SOS support and we can help you through this process.

!!! WARNING
    
    We recommend trying this process in a test environment before attempting in your production environment.

If you have reviewed all of the warnings above and still want to attempt migration, you should be able to do the following.

!!! NOTE
    
    If you have a distributed deployment, you will need to perform the steps on the manager first and then on each of the remaining nodes.

First, make sure that your 2.4 installation is fully updated via [soup](soup.md):

```
sudo soup
```

Next, make sure there is a backup in /nsm/backup:

```
sudo ls -alh /nsm/backup
```

TODO
