##Installation and Configuration

#Prerequisites
4 vCPU
9GB free Memory
80GB free Storage Space

#Operation System Requirements

Windows
Requires the Windows 10 PRO Fall Creators UPdate or newer

MacOS
Requires macOS 10.12 Sierra or newer

Linux
Supported
Rhel/CentOS 7.5 or newer
Fedora -2 Latest stable releases

#Unsupported
Ubuntu 18.04 LTS or newer
Debian 10 or newer

#Required Software Packages
Linux Distribution Installation Commands
Fedora             sudo dnf install NetworkManager
Rhel/CentOS        su -c yum install NetworkManager'
Debian/Ubuntu      sudo apt install qemu-kvm libvirt-daemon libvirt-daemon-system network-manager


###########################################################################
#Installation and Upgrade

Install CodeReady Containers

Step 1 -  Download the latest release
Next extract the contents somewhere in your PATH.

Step 2 - Setup CodeReady Containers
Run the "crc setup" command

Step 3 - Start CodeReady Containers
Run the "crc start" command

========================================================

Upgrade

Step 1 - Download the latest release
Nex extract the contents

Step 2 - Delete the existing VM
Run the "crc delete" command

Step 3 - Replace CRC binary and verify
Run the crc version command

Step 4 - Setup CodeReady Containers and Start Openshift
Do steps 2 and 3 from the installation


################################################################################
#Accessing OpenShift
Step 1 - Add the oc binary to your PATH
Run the crc oc-env to print out the command needed to add oc to your PATH

Step 2 - Log in to Openshift
Run the crc console --credentials to view the login credentials for the cluster.

Step 3 -  Test the oc command
Run the oc get co command to check the OpenShift Cluster Operators

Step 4 - Open web console
Run the oc get console to open console in your default browser


