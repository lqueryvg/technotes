yum install wget
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import http://pkg.jenkins.io/redhat-stable/jenkins.io.key
yum install jenkins
systemctl disable firewalld
systemctl stop firewalld
/usr/sbin/rcjenkins start
cat /var/lib/jenkins/secrets/initialAdminPassword
http://{host}:8080
yum install git
