# SELinux
- The purpose of this project is to run EXPRESSCLUSTER kernel modules wiht SELinux.

## Evaluation Environment
- MIRACLE LINUX 8 Asianux Inside (4.18.0-147.5.1.el8.x86_64)
- EXRESSCLUSTER X 4.2

## How to run EXPRESSCLUSTER with SELinux
### Enable Kernel Modules using Logs of Denined Operations
1. Create a cluster with the following resources.
   - Heartbeat resource
     - Kernel mode heartbeat
   - Group resource
     - Mirror disk resource
   - Monitor resoruce
     - User mode monitor resoruce
       - Method: keepalive
1. Start the cluster and get the following errors in /var/log/messages.
   ```
   platform-python[3292]: SELinux is preventing /usr/bin/kmod from module_load access on the system /opt/nec/clusterpro/drivers/khb/distribution/asianux/clpkhb-4.18.0-147.5.1.el8.x86_64.ko.
   
   *****  Plugin catchall (100. confidence) suggests   **************************
   
   If you believe that kmod should be allowed module_load access on the clpkhb-4.18.0-147.5.1.el8.x86_64.ko system by default.
   Then you should report this as a bug.
   You can generate a local policy module to allow this access.
   Do allow this access for now by executing:
   # ausearch -c 'insmod' --raw | audit2allow -M my-insmod
   # semodule -X 300 -i my-insmod.pp#012
   ```
   ```
   platform-python[3292]: SELinux is preventing /usr/bin/kmod from module_load access on the system /opt/nec/clusterpro/drivers/ka/distribution/asianux/clpka-4.18.0-147.5.1.el8.x86_64.ko.
   
   *****  Plugin catchall (100. confidence) suggests   **************************

   If you believe that kmod should be allowed module_load access on the clpka-4.18.0-147.5.1.el8.x86_64.ko system by default.#012Then you should report this as a bug.
   You can generate a local policy module to allow this access.
   Do allow this access for now by executing:
   # ausearch -c 'insmod' --raw | audit2allow -M my-insmod
   # semodule -X 300 -i my-insmod.pp#012
   ```
1. Stop the cluster.
1. Allow to load clpka.
   ```sh
   # ausearch -c 'insmod' --raw|audit2allow -M clpka
   ******************** IMPORTANT ***********************
   To make this policy package active, execute:
   
   semodule -i clpka.pp
   
   # semodule -i clpka.pp
   ```
1. Allow to load clpkhb.
   ```sh
   # ausearch -c 'insmod' --raw|audit2allow -M clpkhb
   ******************** IMPORTANT ***********************
   To make this policy package active, execute:
   
   semodule -i clpkhb.pp
   
   # semodule -i clpkhb.pp
   ```
1. Start the cluster.
1. Check if the kernel modules are loaded.
   ```sh
   # lsmod
   Module                  Size  Used by
   clpka                  24576  1
   clpkhb                 40960  2 clpka
   liscal                294912  1
    :
   ```
1. Check the cluster status.
   ```sh
   # clpstat
    ========================  CLUSTER STATUS  ===========================
     Cluster : cluster
     <server>
      *ml8-221 .........: Online
         lankhb1        : Normal           Kernel Mode LAN Heartbeat
         lankhb2        : Normal           Kernel Mode LAN Heartbeat
         diskhb1        : Normal           DISK Heartbeat
         witnesshb1     : Normal           Witness Heartbeat
         httpnp1        : Normal           http resolution
         pingnp1        : Normal           ping resolution
       ml8-222 .........: Online
         lankhb1        : Normal           Kernel Mode LAN Heartbeat
         lankhb2        : Normal           Kernel Mode LAN Heartbeat
         diskhb1        : Normal           DISK Heartbeat
         witnesshb1     : Normal           Witness Heartbeat
         httpnp1        : Normal           http resolution
         pingnp1        : Normal           ping resolution
     <group>
       failover1 .......: Online
         current        : ml8-221
         disk1          : Online
         exec1          : Online
         fip-223        : Online
         md1            : Online
     <monitor>
       diskw1           : Normal
       fipw1            : Normal
       mdnw1            : Normal
       mdw1             : Normal
       userw            : Normal
    =====================================================================   
   ```