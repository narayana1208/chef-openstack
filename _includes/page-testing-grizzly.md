# Testing OpenStack

Use the testing procedures below before pushing your server into a production environment. Additionally, in case you want to create your
own testing environment (or sandbox), those instructions are also included below.

## Test Connectivity

The hostname for each server must meet the following requirements:

1.  The hostname must be a fully qualified domain name (FQDN), which includes the domain suffix. Example: mychefserver.example.com (not
  simply mychefserver).
2.  The hostname must be resolvable. In most cases, such as for a server that will run in a production environment, add the hostname for
  the server to the DNS system. In some cases, such as when deploying server into a testing environment, just adding the hostname to the
  /etc/hosts file is enough to ensure that a hostname is resolvable.

### Resolvable Hostname

To verify if a hostname is resolvable, run the following command:

<pre>
$ hostname -f
</pre>

If the hostname is resolvable, it will return something like:

<pre>
mychefserver.example.com
</pre>

Alternatively, you can `ping` the host:

<pre>
$ ping mychefserver.example.com

PING mychefserver.example.com (127.0.0.1) 56(84) bytes of data.
64 bytes from mychefserver.example.com (127.0.0.1): icmp_req=1 ttl=64 time=0.034 ms
64 bytes from mychefserver.example.com (127.0.0.1): icmp_req=2 ttl=64 time=0.028 ms
64 bytes from mychefserver.example.com (127.0.0.1): icmp_req=3 ttl=64 time=0.014 ms
64 bytes from mychefserver.example.com (127.0.0.1): icmp_req=4 ttl=64 time=0.014 ms
</pre>

### Check Connectivity

A simple routine to mark off your checklist is making sure that each server is able to communicate with the rest of the cluster. This is
important when preparing for deployment and for testing afterward. To do this use the ping&nbsp;command to ping the other server's
FQDN.

<pre>
$ ping control1.example.com
$ ping compute2.example.com
$ ping network3.example.com
</pre>

## Test Your Install

Run the following commands on the controller node to check if OpenStack has been deployed and is running correctly. Before running them,
be sure to update your bash environment with the correct OpenStack variables to use them:

<pre>
# source .openrc
</pre>

### Nova

<output><pre>root@control1:~# nova-manage service list 

Binary           Host      Zone             Status     State Updated_At                                                                         
nova-cert        control1  internal         enabled    :-)   2013-09-03 15:21:29
nova-scheduler   control1  internal         enabled    :-)   2013-09-03 15:21:29
nova-conductor   control1  internal         enabled    :-)   2013-09-03 15:21:29
nova-consoleauth control1  internal         enabled    :-)   2013-09-03 15:21:30
nova-compute     compute2  nova             enabled    :-)   2013-09-03 15:21:23
</pre></output>

Each service or agent will display a&nbsp;`:-)`&nbsp;if they are running correctly, and&nbsp;`XX`&nbsp;if they are
not.

### Quantum/Neutron

<output><pre>root@control1:~# quantum agent-list

+--------------------------------------+--------------------+----------+-------+----------------+                                                
| id                                   | agent_type         | host     | alive | admin_state_up |
+--------------------------------------+--------------------+----------+-------+----------------+
| 438d2dd2-daf3-496c-99ad-179ed307b8d6 | Open vSwitch agent | compute2 | :-)   | True           |
| 4c684b66-16db-4853-8cb3-51098c3752b3 | L3 agent           | network3 | :-)   | True           |
| 98c3d818-0ebe-4cd6-89bf-0f107a7532ab | DHCP agent         | network3 | :-)   | True           |
| dd3b9134-ab76-440a-ba7a-70135202eb82 | Open vSwitch agent | network3 | :-)   | True           |
+--------------------------------------+--------------------+----------+-------+----------------+
</pre></output>

Each service or agent will display a `:-)` if they are running correctly, and&nbsp;`XX` if they are not.

### Glance

<output><pre class="nowrap"><div style="width:828px; overflow-x: scroll; overflow-y: hidden;">root@control1:~# glance image-list

+--------------------------------------+-----------------------------------+-------------+------------------+-----------+--------+
| ID                                   | Name                              | Disk Format | Container Format | Size      | Status |
+--------------------------------------+-----------------------------------+-------------+------------------+-----------+--------+
| 3470a8b5-46a7-442b-ac75-7c8f663e271d | CirrOS 0.3.0 i386                 | qcow2       | bare             | 9159168   | active |
| ace57487-30b6-41c9-9e7a-9d44f103437d | CirrOS 0.3.0 x86_64               | qcow2       | bare             | 9761280   | active |
| 3722fed6-0065-4521-b480-8abd5f7abf2c | Fedora 18 (Cloud) i386            | qcow2       | bare             | 226492416 | active |
| 21c3f3ae-f773-46f9-8fef-d3c0a712ef45 | Fedora 18 (Cloud) x86_64          | qcow2       | bare             | 228196352 | active |
| dbda560c-8e09-4035-9332-03ef4470a934 | Fedora 19 (Cloud) i386            | qcow2       | bare             | 235536384 | active |
| c2ac12e3-5f11-4679-8074-232c5040b901 | Fedora 19 (Cloud) x86_64          | qcow2       | bare             | 237371392 | active |
| 2baacb65-fa9d-4707-9856-5b6d5803d63e | Ubuntu 12.04 Server (Cloud) amd64 | qcow2       | bare             | 251985920 | active |
| 49a33caa-8e78-41c3-8af6-cc5ea25182f2 | Ubuntu 12.04 Server (Cloud) i386  | qcow2       | bare             | 230621184 | active |
| 5b8998ce-40fa-43cb-9f79-11f4e9a32296 | Ubuntu 12.10 Server (Cloud) amd64 | qcow2       | bare             | 221642752 | active |
| e8f69a8b-c1f5-4432-89bb-6bfecfea7cc3 | Ubuntu 12.10 Server (Cloud) i386  | qcow2       | bare             | 219938816 | active |
| 668e92c6-b6c0-4f9e-bf0d-078ede9667e9 | Ubuntu 13.04 Server (Cloud) amd64 | qcow2       | bare             | 235143168 | active |
| 169fc708-cdbf-4dd3-a106-ced48a88922f | Ubuntu 13.04 Server (Cloud) i386  | qcow2       | bare             | 233308160 | active |
+--------------------------------------+-----------------------------------+-------------+------------------+-----------+--------+
</div></pre></output>

### Cinder

If you haven't created any Cinder volumes yet, this command will return nothing. Otherwise, a list of created volumes will be
returned.

<output><pre class="nowrap"><div style="width:828px; overflow-x: scroll; overflow-y: hidden;">root@control1:~# cinder list

+--------------------------------------+-----------+--------------+------+-------------+----------+-------------+
|                  ID                  |   Status  | Display Name | Size | Volume Type | Bootable | Attached to |
+--------------------------------------+-----------+--------------+------+-------------+----------+-------------+
| e60def40-03a0-4e08-a9e5-3f60be89ad57 | available |     test     |  1   |     None    |  false   |             |
+--------------------------------------+-----------+--------------+------+-------------+----------+-------------+
</div></pre></output>

### Keystone

Keystone authentication has several commands that are worth checking, especially if you are experience any difficulty with user
accounts, OpenStack component communication, or authentication problems.

For detailed information on troubleshooting and debugging Keystone, please refer to its documentation at [http://docs.openstack.org](http://docs.openstack.org/).

#### Check Keystone Users

<output><pre>root@control1:~# keystone user-list

+----------------------------------+----------+---------+--------------------+
|                id                |   name   | enabled |       email        |
+----------------------------------+----------+---------+--------------------+
| 36fcdb3ca5d349ffb82731b91c522080 |  admin   |   True  |   root@localhost   |
| 01b0af86bb1e4062b04882a03bff9758 |  cinder  |   True  |  cinder@localhost  |
| 2e15846831884959a6fc0be96a9bceaf |   demo   |   True  |   demo@localhost   |
| ee1ffd860b9940c9bedcbd27e5439333 |  glance  |   True  |  glance@localhost  |
| 62a9c425721f48d8892a9132e23ede03 |   nova   |   True  |   nova@localhost   |
| b22df525c6714c259189fd2da82b72af | quantum  |   True  | quantum@localhost  |
+----------------------------------+----------+---------+--------------------+
</pre></output>

#### Check Keystone Tenants

<output><pre>root@control1:~# keystone tenant-list

+----------------------------------+--------------------+---------+
|                id                |        name        | enabled |
+----------------------------------+--------------------+---------+
| a06ad73e633b4a479986f8de4b613e51 |       admin        |   True  |
| bb4f6b6e17314255b49a39fbe9fdff58 |        demo        |   True  |
| be05a0ac2e034f3aa43ac06a90943a14 | invisible_to_admin |   True  |
| cc0804251a5145c485ad87ab384350be |      service       |   True  |
+----------------------------------+--------------------+---------+
</pre></output>

#### Check Keystone Services

<output><pre>root@control1:~# keystone service-list

+----------------------------------+----------+----------+------------------------------+
|                id                |   name   |   type   |         description          |
+----------------------------------+----------+----------+------------------------------+
| fd9b4f0ed7434504a8938d6e8b674455 |  cinder  |  volume  |   OpenStack Volume Service   |
| 05a89640b8d04a5c902b86d4bfbb9a8f |   ec2    |   ec2    |    OpenStack EC2 service     |
| 844e120b3b594f68838e6b0e5a227427 |  glance  |  image   |   OpenStack Image Service    |
| 1ef9095e84d44658a66a46aabe4ef551 | keystone | identity |      OpenStack Identity      |
| 8f50ec8454b9432b94cd7d68172dc98a |   nova   | compute  |  OpenStack Compute Service   |
| 3de9a5a7e57943739f59c014554ea602 | quantum  | network  | OpenStack Networking service |
+----------------------------------+----------+----------+------------------------------+
</pre></output>

Keystone endpoints are extremely important and can cripple your deployment when not configured correctly. However, Chef wires up the
necessary endpoint information correctly during deployment. In any event, checking the endpoint list to make sure it's correct is always a
good idea when troubleshooting issues connecting or authenticating to Keystone. Each endpoint should correspond to the correct server in
your cluster. Note that the ports in the example below are the defaults, and may vary if you've overridden the default attributes for
them.

<output><pre class="nowrap"><div style="width:828px; overflow-x: scroll; overflow-y: hidden;">root@openstack1:~# keystone endpoint-list

+----------------------------------+---------+-------------------------------------------+-------------------------------------------+-------------------------------------------+----------------------------------+
|                id                |  region |                 publicurl                 |                internalurl                |                  adminurl                 |            service_id            |
+----------------------------------+---------+-------------------------------------------+-------------------------------------------+-------------------------------------------+----------------------------------+
| 0a9d0209521e40c4820c224e3e1a015d | Region  |       http://XX.XX.XX.XX:5000/v2.0        |       http://XX.XX.XX.XX:5000/v2.0        |       http://XX.XX.XX.XX:35357/v2.0       | 1ef9095e84d44658a66a46aabe4ef551 |
| 33766366fb4e4e919d441116f46a6900 | Region  | http://XX.XX.XX.XX:8776/v1/$(tenant_id)s  | http://XX.XX.XX.XX:8776/v1/$(tenant_id)s  | http://XX.XX.XX.XX:8776/v1/$(tenant_id)s  | fd9b4f0ed7434504a8938d6e8b674455 |
| 4d6f3fb4758442068e73b808a0501864 | Region  |  http://XX.XX.XX.XX:8773/services/Cloud   |  http://XX.XX.XX.XX:8773/services/Cloud   |  http://XX.XX.XX.XX:8773/services/Admin   | 05a89640b8d04a5c902b86d4bfbb9a8f |
| 87343c685ef5477b81075017159e1a39 | Region  |         http://XX.XX.XX.XX:9696/          |         http://XX.XX.XX.XX:9696/          |         http://XX.XX.XX.XX:9696/          | 3de9a5a7e57943739f59c014554ea602 |
| e81953d02428499bb981e78be560bc88 | Region  |        http://XX.XX.XX.XX:9292/v2         |        http://XX.XX.XX.XX:9292/v2         |        http://XX.XX.XX.XX:9292/v2         | 844e120b3b594f68838e6b0e5a227427 |
| f560ac6915f241019b95e103146326dd | Region  | http://XX.XX.XX.XX:8774/v2/$(tenant_id)s  | http://XX.XX.XX.XX:8774/v2/$(tenant_id)s  | http://XX.XX.XX.XX:8774/v2/$(tenant_id)s  | 8f50ec8454b9432b94cd7d68172dc98a |
+----------------------------------+---------+-------------------------------------------+-------------------------------------------+-------------------------------------------+----------------------------------+
</div></pre></output>