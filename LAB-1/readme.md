<h1>Deploying an Enterprise Website on Huawei Cloud</h1>

<p></strong>Lab Guide: Deploying an Enterprise Website on Huawei Cloud.</strong></p>

<p>This lab walks you through deploying a high-availability WordPress website on <strong>HUAWEI CLOUD</strong>, featuring separate ECS instances for databases and services, Elastic Load Balancer (ELB), and Auto Scaling (AS).</p>

<h2>1. Requirements</h2>
<ul>
  <li>Database and service nodes must be on separate ECSs.</li>
  <li>ECSs should automatically scale with traffic.</li>
</ul>

<h2>2. Prerequisites</h2>
<p>
Log in to <strong>Huawei Cloud</strong> using IAM credentials. Use the Lab Desktop’s Chrome browser, select <strong>IAM User Login</strong>, and enter lab-assigned credentials.
</p>

<h2>3. Task Overview</h2>

<h3>3.1 Create a Virtual Private Cloud (VPC)</h3>
<ol>
  <li>Navigate to <em>Service List > Networking > Virtual Private Cloud</em>.</li>
  <li>Click <strong>Create VPC</strong> and set:
    <ul>
      <li><strong>Region:</strong> AP-Singapore</li>
      <li><strong>Name:</strong> vpc-mp</li>
    </ul>
  </li>
</ol>

<h3>3.2 Create and Configure a Security Group</h3>
<ol>
  <li>Go to <em>Access Control > Security Groups</em>, create a group.</li>
  <li>Add an inbound rule:
    <ul>
      <li><strong>Protocol & Port:</strong> All</li>
      <li><strong>Source IP:</strong> 0.0.0.0/0</li>
    </ul>
  </li>
</ol>

<h3>3.3 Purchase ECS</h3>
<ol>
  <li>Navigate to <em>Compute > Elastic Cloud Server</em> and click <strong>Buy ECS</strong>.</li>
  <li>Set:
    <ul>
      <li><strong>Billing Mode:</strong> Pay-per-use</li>
      <li><strong>Specs:</strong> s6.small.1 (1 vCPU, 1GB RAM)</li>
      <li><strong>Image:</strong> CentOS 7.6 64bit</li>
      <li><strong>EIP:</strong> Auto assign, 2 Mbps</li>
    </ul>
  </li>
</ol>

<h3>3.4 Buy RDS Instance</h3>
<ol>
  <li>Go to <em>Database > Relational Database Service</em>, click <strong>Buy DB Instance</strong>.</li>
  <li>Set:
    <ul>
      <li><strong>Type:</strong> MySQL 8.0</li>
      <li><strong>Specs:</strong> 2 vCPU, 4 GB RAM</li>
      <li><strong>Security Group:</strong> Use existing group</li>
    </ul>
  </li>
</ol>

<h2>4. Deploy LAMP Stack & WordPress</h2>

<h3>4.1 Install LAMP on ECS</h3>
<ol>
  <li>Use Remote Login and run:
    <code>yum install -y httpd php php-fpm php-mysql mysql</code></li>
  <li>Edit <code>httpd.conf</code> and add <code>ServerName localhost:80</code>.</li>
  <li>Download and extract WordPress:
    <ul>
      <li><code>wget https://.../wordpress-4.9.10.tar.gz</code></li>
      <li><code>tar -zxvf wordpress-4.9.10.tar.gz -C /var/www/html</code></li>
    </ul>
  </li>
  <li>Run and enable services:
    <ul>
      <li><code>systemctl start httpd</code></li>
      <li><code>systemctl start php-fpm</code></li>
      <li><code>systemctl enable httpd</code></li>
      <li><code>systemctl enable php-fpm</code></li>
    </ul>
  </li>
</ol>

<h3>4.2 Create MySQL Database</h3>
<ol>
  <li>Log into RDS Console and open <strong>SQL Operations</strong>.</li>
  <li>Execute: <code>CREATE DATABASE wordpress;</code></li>
</ol>

<h3>4.3 Complete WordPress Installation</h3>
<ol>
  <li>Go to <code>http://ECS-EIP/wordpress</code> in your browser.</li>
  <li>Enter database credentials and click <strong>Run the installation</strong>.</li>
</ol>

<h2>5. Enable High Availability</h2>

<h3>5.1 Set Up Load Balancer (ELB)</h3>
<ol>
  <li>Go to <em>Networking > Elastic Load Balance</em> and buy a shared ELB.</li>
  <li>Configure listener and backend server group (disable Health Check).</li>
</ol>

<h3>5.2 Create ECS Image</h3>
<ol>
  <li>Stop the running ECS and create a system disk image in <em>Image Management Service</em>.</li>
</ol>

<h3>5.3 Configure Auto Scaling (AS)</h3>
<ol>
  <li>Go to <em>Compute > Auto Scaling</em> and:
    <ul>
      <li>Create AS Configuration using the custom image</li>
      <li>Create AS Group and attach ELB</li>
      <li>Add AS Policy:
        <ul>
          <li><strong>Scale Out:</strong> CPU ≥ 60%</li>
          <li><strong>Scale In:</strong> CPU ≤ 20%</li>
        </ul>
      </li>
    </ul>
  </li>
</ol>

<h2>6. Access and Verify</h2>
<p>Visit <code>http://<em>LoadBalancer-EIP</em>/wordpress/</code> to check website deployment. If the site loads, HA setup and scaling policies are working as expected.</p>

<h2>7. Monitor Resources</h2>
<ol>
  <li>Navigate to <em>Management & Governance > Cloud Eye</em>.</li>
  <li>Check:
    <ul>
      <li><strong>Overview:</strong> Resource stats</li>
      <li><strong>Alarm Records:</strong> System alerts</li>
      <li><strong>Server Monitoring:</strong> ECS-level metrics</li>
    </ul>
  </li>
</ol>

<p><strong>✔️ Congratulations!</strong> You’ve successfully deployed a scalable, cloud-based WordPress environment using Huawei Cloud services.</p>

</body>
