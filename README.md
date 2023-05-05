Download Link: https://assignmentchef.com/product/solved-ece1508-assignment-5
<br>
In this lab, you will learn how to programmatically deploy your own custom network topology as an overlay network on top of the SAVI testbed. Similar to Mininet, you will be able to specify the number of end-hosts and switches in your network, as well as how they are connected together to create dynamic topologies. This is the first lab of a two-part series, so your work in this lab will lay a critical foundation for the next lab.

This lab will use both the command line interface (CLI) tools for interacting with the SAVI testbed. For Windows users, a few tools (free to download) that may be helpful for interfacing with Linux-based virtual machines are listed here as follows:

<ul>

 <li>PuTTY: Free terminal emulator for Windows, for logging into Unix-based systems; o Tutorial: <a href="http://www.electrictoolbox.com/article/applications/ssh-putty/">http://www.electrictoolbox.com/article/applications/ssh-putty/</a></li>

 <li>WinSCP: Free Windows SCP client for transferring files to/from Unix-based systems. o Tutorial: <a href="https://sysmincomputing.wordpress.com/2011/01/22/winscp/">https://sysmincomputing.wordpress.com/2011/01/22/winscp/</a></li>

</ul>

If you’re using a Mac or Linux based operating system, similar tools should already exist and ready for you to use from your Terminal interface. If anyone needs help in using these tools, please feel free to post a message in the discussion board on Piazza.

Also, a helpful hint for anyone unfamiliar with Unix-based terminals: most terminals have a history buffer, which stores a list of previous commands. You can scroll through this history via the up and down arrow keys on your keyboard.

<h1>Prerequisite: Accessing the SAVI Command-Line Client Tools</h1>

You have all been assigned a guest account on SAVI. To find your SAVI credentials, please access the following link: <a href="https://tinyurl.com/ece1508-2019s">https://tinyurl.com/ece1508-2019s</a>




A few scripts have been made and are provided to you in order to simplify some of the tasks in this lab. There is a gateway server set up with all the CLI client tools needed to access SAVI, and all the scripts already installed. You can log into the server via SSH:

<ul>

 <li><em>ssh {username}@client1.savitestbed.ca </em>o The <em>username</em> is your SAVI username, and the default password is the same as your username (or whatever you changed it to).</li>

</ul>




After you have logged in, you must load the proper credentials into your environment variables. A script has been prepared for you in your home directory called <em>savi</em>. The usage of the script is as follows:

<ul>

 <li><em>source savi {username} {project} {node} </em>o The <em>username</em> is your SAVI username

  <ul>

   <li>The <em>project</em> is the project/tenant that was assigned to you</li>

   <li>Specify the <em>node</em> depending on the region assigned to you in the credentials sheet</li>

  </ul></li>

</ul>




You are now ready to use the SAVI testbed. To test, please type <em>nova image-list</em> to see if you can view the list of available VM images.




<strong>             </strong>

<h1>Part 1: Basic Overlay Network Orchestrator</h1>

You will complete a script, named <em>saviOverlay</em>, which will allow you to dynamically create

SDN-enabled overlay networks of your choice on any cloud infrastructure that supports OpenStack APIs. Overlays are a way of realizing logical topologies that exist on top of an underlying network topology<a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a>. You can execute the script (written in Python) from within any Linux environment, but for this lab you will be using the SAVI client server. In this part of the lab, the end-goal is to be able to boot up a simple overlay network and test its connectivity via ICMP pings.




The topology you will create is shown in Figure 1 below. Altogether, this overlay requires 4 VMs to be created. By default, the switches (Open vSwitch or OVS) function as simple L2 Ethernet learning switches, so connectivity should be plug-and-play.




<strong>Figure 1: Overlay Topology for Part 1 </strong>




The overlay connections are created using VXLAN tunnels. VXLAN is a protocol that enables the virtualization of networks by supporting packet encapsulations of Layer 2 frames within Layer 4 UDP packets. This can be seen in Figure 2 below.




<strong>Figure 2: VXLAN Encapsulation<a href="#_ftn2" name="_ftnref2"><sup>[2]</sup></a> </strong>




To help you get started by setting up the necessary SSH key-pairs, security group rules, and download the required files for this lab, you can execute the initialization script: • <em>netsoft-overlay-init </em>




You should see a new <em>programmable_networks_lab</em> directory within your home directory, and within that, there should exist the <em>saviOverlay</em> script, as well as a file named <em>overlay_config.py</em>. Note that this filename is important for the script to work (i.e. the script searches for a file named <em>overlay_config.py</em>). If you change the name of this file, you will have to change the script accordingly. You may want to open the files and observe the contents inside in order to learn how they work together. You should also double-check the security group, which was created with your username, to ensure it has rues for opening TCP ports 22, and 6633, UDP port 4789, and enabling all ICMP.




<u>Tasks to Complete</u>

<ol>

 <li>Go into the <em>programmable_networks_lab</em> directory, if you haven’t already, and try executing the <em>saviOverlay</em> You should see a help section printed out.

  <ul>

   <li>Three functional options are available as a second argument for the script. They are briefly described here below:

    <ul>

     <li><strong>deploy</strong>: Creates the overlay. This involves the full operation of creating the VMs, and SSH’ing into them to configure OVS to set up the overlay connections;</li>

     <li><strong>list</strong>: Lists the VMs belonging to your overlay; and o <strong>cleanup</strong>: Deletes the VMs belonging to your overlay.</li>

    </ul></li>

   <li>Right now, these options won’t do anything except print out a simple message.</li>

  </ul></li>

 <li>The initialization script you ran earlier has already configured the <em>py</em> file for you. Open it and double-check the settings.

  <ul>

   <li>The default topology creates the 4-VM (2 host and 2 switches) topology as shown in class. Keep this for now, and when your script fully works, you can test with other topologies.</li>

   <li><strong>NOTE</strong>: There is a field called <em>contr_addr</em> which will be used in the second part of this lab. You can ignore this field for now.</li>

  </ul></li>

 <li>Open up the <em>saviOverlay</em> script in whichever editor you prefer, and study the code. This script is a skeleton, meaning it has the basic structure already set up. It is your job to understand the code written thus far, and complete it.

  <ul>

   <li><strong>NOTE</strong>: To open the script, you can use either command-line editors like <em>vim</em> or <em>nano</em>, or copy the file to/from your computer and edit it locally. If you choose the file copying method, keep in mind that you need to copy twice (i.e. once from the VM to <em>client1</em>, then from client1 to your computer).</li>

  </ul></li>

 <li>There are 6 functions within the script which you must complete, some of them may already include some code within them. The 6 functions are:

  <ul>

   <li><strong>bootVM()</strong>: Creates a new virtual machine. The existing code within the function will pre-pend your username to any VM name that is passed into the function, this is how you can identify which VM belongs to you and your overlay;</li>

   <li><strong>setOverlayInterface()</strong>: SSH into a given VM and creates an internal interface on the OVS, and sets the overlay IP address on the interface;</li>

   <li><strong>connectNodes()</strong>: Establishes bi-directional VXLAN connectivity between the OVS switches within two VMs. This involves SSH’ing into the VMs and creating the VXLAN interfaces on the OVS;</li>

   <li><strong>deployOverlay()</strong>: Called directly from the script’s main function. Reads the topology from the <em>py</em> file and calls the appropriate functions to create and configure the overlay network;</li>

   <li><strong>listOverlay()</strong>: Called directly from the script’s main function. Lists the VMs belonging to the overlay network, by printing out the VM’s names, UUIDs, and underlay IP addresses; and</li>

   <li><strong>cleanupOverlay()</strong>: Called directly from the script’s main function. Finds the VMs belonging to the overlay network and deletes them.</li>

  </ul></li>

</ol>




The first three functions are helper functions. The last three are functions directly invoked by the script’s main, and can call the helper functions if need be.




<strong>NOTE</strong>: It is highly recommended that you study the comments of these functions before starting, as there may be hints on how you should use them or complete them.

<ol start="5">

 <li>You will undoubtedly run the script numerous times during your testing process. Between each iteration, please clean-up your previous VMs to avoid needlessly taking up resources (the cloud is not an infinite pool of resources). To help in this process, it is recommended that you complete the <em>cleanupOverlay()</em> function as soon as possible.</li>

</ol>




<strong>NOTE</strong>: You are sharing the tenant with other people in the course. Be careful not to delete other people’s VMs!




You can verify the deletion of your VMs by typing <em>nova list</em> and seeing if any VMs related to your overlay still exist.

<ol start="6">

 <li>Modify the topology to ensure that your script can handle arbitrary configurations. The new topology is entirely up to you. The only limitation is to ensure that you do not introduce loops into the topology.</li>

 <li>At the end, clean up and delete your overlay network by simply running: <em>./saviOverlay cleanup </em></li>

</ol>




<h1>Part 2: SDN-Enabled Overlay Network</h1>

By default, the overlay works without having to install any flows into the switches due to the fact that the default behaviour of OVS is to function as a basic L2 Ethernet learning switch. In this part of the lab, you will connect the OVS within the switch nodes of your overlay topology to connect to a remote SDN controller, as depicted in Figure 3 below.




<strong>Figure 3: Overlay Topology Controlled by an SDN Controller </strong>




<u>Tasks to Complete</u>

<ol>

 <li>To serve the OpenFlow controller of the overlay network we will create, you need to first boot up a VM based on the “<strong>ece361</strong>” image using the “<strong>small</strong>” flavor. Use the key and security group that was created for you when you ran the init script.

  <ul>

   <li>If you don’t remember the key and security groups that were created for you by the init script, you can find them by running:</li>

  </ul></li>

</ol>

o <em>nova keypair-list </em>o <em>nova secgroup-list </em>

<ul>

 <li>Refer back to the lecture slides if you’re unsure how to create a VM.</li>

 <li>The ece361 image comes with a light-weight Python-based OpenFlow controller, named Ryu, already installed. It also includes a Python library which you will use later on in the next lab.</li>

</ul>

<ol start="2">

 <li>Access the controller VM via SSH. The default username/password is: <em>ubuntu</em>/<em>ece361</em> To update the controller code, run <em>ece361-update</em> once you’ve logged into the VM 3. To start up the Ryu OpenFlow controller, execute the following command: • <em>ece361-start-controller switch</em></li>

</ol>




When you run the command, it starts Ryu within a <em>screen</em> in the background. You can search online how to attach and detach to this screen. Ryu is launched using the settings written into the configuration file specified (you don’t need to worry about this) and loads a L2 learning switch application defining the behaviour of connected switches.

<ol start="4">

 <li>The initialization script you ran earlier has already configured most of the settings in <em>py</em> file for you. However, you need to modify it further to specify the IP address and port of the SDN controller.</li>

</ol>

<ul>

 <li>The <em>contr_addr</em> variable should be modified and set. For example, if the VM created has an IP of 10.2.0.3 and the SDN controller is listening on port 6633 (which it should be), then set the variable to <em>“10.2.0.3:6633”</em>.</li>

</ul>

<ol start="5">

 <li>Open the <em>saviOverlay</em> script and note the pre-defined function called <em>setController()</em>. Modify your <em>deployOverlay()</em> code to call <em>setController()</em> such that the OVS’ in the switch nodes of the topology are connected to the controller.</li>

 <li>Re-deploy your overlay and test that it still has connectivity from h1 to h2.</li>

 <li>At the end, clean up and delete your overlay network by simply running: <em>./saviOverlay cleanup </em></li>

</ol>




<h1>Post-Lab: Cleaning Your Activity</h1>

After you are done the lab, please delete any leftover VMs you have created (overlay and SDN controller), as well as any security groups you may have created.




<h1>Appendix A: Nova Client Walkthrough import novaclient.v1_1.client as novaClient</h1>




# Setting credentials username <strong>=</strong> ‘your_username’ password <strong>=</strong> ‘your_password’ tenant_name <strong>=</strong> ‘your_tenant_project’ reg <strong>=</strong> ‘your_assigned_region’

auth_url <strong>=</strong> ‘http://iam.savitestbed.ca:5000/v2.0/’




nova <strong>=</strong> novaClient<strong>.</strong>Client<strong>(</strong>username<strong>,</strong> password<strong>,</strong> tenant_name<strong>,</strong> auth_url<strong>,</strong>                                     region_name<strong>=</strong>reg<strong>,</strong> no_cache<strong>=</strong><strong>True</strong><strong>)</strong>




# Equivalent to “nova list” in command-line nova<strong>.</strong>servers<strong>.</strong>list<strong>()</strong>




# Equivalent to “nova flavor-list” in command-line nova<strong>.</strong>flavors<strong>.</strong>list<strong>()</strong>




# Note how they return lists of objects




# Get an item (the first object in the list) and inspect it a <strong>=</strong> nova<strong>.</strong>flavors<strong>.</strong>list<strong>()[</strong>0<strong>]</strong>




dir<strong>(</strong>a<strong>)</strong> # Alternatively, type “a.” then press tab




# We see the object has a member variable called ‘name’

# Let’s use the find() function to search based on ‘name’ flavor <strong>=</strong> nova<strong>.</strong>flavors<strong>.</strong>find<strong>(</strong>name <strong>=</strong> “m1.small”<strong>)</strong>




# Equivalent to “nova image-list” in command-line nova<strong>.</strong>images<strong>.</strong>list<strong>()</strong>




# Get an item (the first object in the list) and inspect it a <strong>=</strong> nova<strong>.</strong>images<strong>.</strong>list<strong>()[</strong>0<strong>]</strong>




dir<strong>(</strong>a<strong>)</strong> # Alternatively, type “a.” then press tab




# We see the object has a member variable called ‘name’

# Let’s use the find() function to search based on ‘name’ image <strong>=</strong> nova<strong>.</strong>images<strong>.</strong>find<strong>(</strong>name <strong>=</strong> ‘ECE1508-overlay’<strong>)  </strong>

# Nova Python client can do limited network commands too… # Equivalent to “neutron net-list” in command-line nova<strong>.</strong>networks<strong>.</strong>list<strong>()  </strong>

# Get an item (the first object in the list) and inspect it a <strong>=</strong> nova<strong>.</strong>networks<strong>.</strong>list<strong>()[</strong>0<strong>] </strong>

<strong> </strong>dir<strong>(</strong>a<strong>)</strong> # Alternatively, type “a.” then press tab # There’s no member variable called name… &#x1f641;

# But wait! We see the object has a member variable called ‘label’

a<strong>.</strong>label # It’s the name!

# Get our network object

# In SAVI, we named all our networks as just the tenant name + ‘-net’ net_name <strong>=</strong> tenant_name <strong>+</strong> ‘-net’




# Let’s use the find() function to search based on ‘label’ net <strong>=</strong> nova<strong>.</strong>networks<strong>.</strong>find<strong>(</strong>label <strong>=</strong> net_name<strong>)</strong>




# How to create a server? nova<strong>.</strong>servers<strong>.</strong>create<strong>()</strong> # Will tell you it needs 4 arguments




# Let’s inspect the function <strong>import</strong> inspect

inspect<strong>.</strong>getargspec<strong>(</strong>nova<strong>.</strong>servers<strong>.</strong>create<strong>)</strong>




# NICs just specifies what network to connect the VM to

vm <strong>=</strong> nova<strong>.</strong>servers<strong>.</strong>create<strong>(</strong>“myTestVM”<strong>,</strong> image<strong>,</strong> flavor<strong>,</strong> key_name<strong>=</strong>‘tlin-client’<strong>,</strong>                         security_groups<strong>=[</strong>‘default’<strong>],</strong> nics<strong>=[{</strong>‘net-id’<strong>:</strong> net<strong>.</strong>id<strong>}])</strong>




# See if it’s active vm<strong>.</strong>status




# Right now will have static object, won’t be refreshed automatically vm<strong>.</strong>get<strong>()</strong> # Re-sync state from Nova







# We need to do this programmatically… let’s loop and wait <strong>import</strong> time

<strong>while</strong> vm<strong>.</strong>status <strong>!=</strong> ‘ACTIVE’<strong>:</strong>     <strong>print</strong> “waiting for status active, sleeping…”     time<strong>.</strong>sleep<strong>(</strong>3<strong>)</strong> # Sleep 3 seconds     vm<strong>.</strong>get<strong>()</strong>




# to SSH into the server and run commands…

<strong>import</strong> paramiko




# More docs here: http://docs.paramiko.org/en/2.4/api/client.html ssh <strong>=</strong> paramiko<strong>.</strong>SSHClient<strong>()</strong> ssh<strong>.</strong>set_missing_host_key_policy<strong>(</strong>paramiko<strong>.</strong>AutoAddPolicy<strong>())  </strong>

# Need to get the IP of the VM ip <strong>=</strong> vm<strong>.</strong>networks<strong>.</strong>get<strong>(</strong>net_name<strong>)[</strong>0<strong>]</strong>




# Let’s SSH! ssh<strong>.</strong>connect<strong>(</strong>ip<strong>,</strong> username <strong>=</strong> ‘ubuntu’<strong>,</strong> password <strong>=</strong> ‘savi’<strong>) </strong>













<table width="624">

 <tbody>

  <tr>

   <td width="624">


    <table width="576">

     <tbody>

      <tr>

       <td colspan="10" width="576"># If the VM’s SSH daemon isn’t up yet, connect() will throw an exception</td>

      </tr>

      <tr>

       <td colspan="7" width="408"># We need to be able to catch this… and try again</td>

       <td colspan="3" rowspan="3" width="168"></td>

      </tr>

      <tr>

       <td colspan="2" width="88"><strong>while</strong> <strong>True</strong><strong>:</strong></td>

       <td colspan="5" rowspan="2" width="320"></td>

      </tr>

      <tr>

       <td width="64">    <strong>try</strong><strong>:</strong></td>

       <td width="24"></td>

      </tr>

      <tr>

       <td colspan="9" width="536">        # The ECE1508-overlay image’s default username and password</td>

       <td rowspan="7" width="40"></td>

      </tr>

      <tr>

       <td colspan="8" width="504">        ssh<strong>.</strong>connect<strong>(</strong>ip<strong>,</strong> username <strong>=</strong> ‘ubuntu’<strong>,</strong> password <strong>=</strong> ‘savi’<strong>)</strong></td>

       <td rowspan="6" width="32"></td>

      </tr>

      <tr>

       <td colspan="3" width="104">        <strong>break</strong></td>

       <td colspan="5" width="400"></td>

      </tr>

      <tr>

       <td colspan="6" width="208">    <strong>except</strong> Exception <strong>as</strong> e<strong>:</strong></td>

       <td colspan="2" rowspan="2" width="296"></td>

      </tr>

      <tr>

       <td colspan="4" width="120">        <strong>print</strong> e</td>

       <td colspan="2" width="88"></td>

      </tr>

      <tr>

       <td colspan="7" width="408">        <strong>print</strong> “Unable to connect. Trying again….”</td>

       <td rowspan="2" width="96"></td>

      </tr>

      <tr>

       <td colspan="5" width="168"></td>

       <td colspan="2" width="240"></td>

      </tr>

      <tr>

       <td width="64"></td>

       <td width="24"></td>

       <td width="16"></td>

       <td width="16"></td>

       <td width="48"></td>

       <td width="40"></td>

       <td width="200"></td>

       <td width="96"></td>

       <td width="32"></td>

       <td width="40"></td>

      </tr>

     </tbody>

    </table>time<strong>.</strong>sleep<strong>(</strong>3<strong>)</strong>


    <table width="496">

     <tbody>

      <tr>

       <td colspan="2" width="496"># Now we’ve established an SSH connection, we can run commands</td>

      </tr>

      <tr>

       <td width="416"></td>

       <td width="80"></td>

      </tr>

     </tbody>

    </table>stdin<strong>,</strong> stdout<strong>,</strong> stderr <strong>=</strong> ssh<strong>.</strong>exec_command<strong>(</strong>“ifconfig”<strong>)</strong> # Get all the output at once out <strong>=</strong> stdout<strong>.</strong>readlines<strong>()</strong> # Kinda ugly, it’s array of strings, one line per element <strong>print</strong> out # Let’s joing them all into one single long string out <strong>=</strong> ”<strong>.</strong>join<strong>(</strong>out<strong>)</strong>  <strong>print</strong> out  # Once you’re done, delete the VM vm<strong>.</strong>delete<strong>() </strong> </td>

  </tr>

 </tbody>

</table>













<a href="#_ftnref1" name="_ftn1">[1]</a> See this figure for example: http://www.ktword.co.kr/img_data/3481.gif

<a href="#_ftnref2" name="_ftn2">[2]</a> From Intel’s blog about VXLAN: https://software.intel.com/en-us/blogs/2015/01/29/optimizing-the-virtualnetworks-with-vxlan-overlay-offloading