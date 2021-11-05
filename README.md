# jenkins-install-kube-deployment
jenkins install step

create instance ubunu 20.04 or 18.04
- run install openvpn
    sudo apt-get update 
    sudo apt install openvpn -y   

    ***if can't install 
      sudo apt-get update 
      sudo apt-get upgrade -y 
- create <filename>.ovpn
    vim <filename>.ovpn
    sample : vim client.ovpn

    ***copy all data from file*.ovpn and insert to client.ovpn

- start openvpn 
    sudo openvpn --config client.ovpn
        ***run in backgroud use : sudo openvpn --config client.ovpn --daemon
    insert username and password

- verify connection
    ip 4 show tun0 
        : if connection success display 
        22: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UNKNOWN group default qlen 100
            link/none
            inet 10.81.234.16/24 brd 10.81.234.255 scope global tun0
                valid_lft forever preferred_lft forever
            inet6 fe80::d745:f537:e929:c837/64 scope link stable-privacy
                valid_lft forever preferred_lft forever
    
    ref:https://tecadmin.net/install-openvpn-client-on-ubuntu/

- install docker 
    sudo apt-get update
    sudo apt-get install \
            ca-certificates \
            curl \
            gnupg \
            lsb-release

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

    echo \
        "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
        $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

    sudo apt-get update
    sudo apt-get install docker-ce docker-ce-cli containerd.io
    sudo docker ps -a

    ref:https://docs.docker.com/engine/install/ubuntu/

- install container jenkins
    sudo docker run -d -u 0 --name jenkins -p 8080:8080 -p 50000:50000 -v /var/run/docker.sock:/var/run/docker.sock -v $(which docker):/usr/bin/docker -v /home/jenkins_home:/var/jenkins_home jenkins/jenkins:lts-jdk11

- setup jenkins from web browser
    http://<instance-ip>:8080
    in terminal : sudo docker logs jenkins
    copy Admin Password from line
        -------------------------------------------------------------------------------------------
        Jenkins initial setup is required. An admin user has been created and a password generated.
        Please use the following password to proceed to installation:

copy->  a89edc55f5a4448faa2bc6f42b07a23a
        -------------------------------------------------------------------------------------------
    install recommend plugins
    setup username and password

- install optional plugins
    Manage Jenkins > Manage Plugins
    click Available and select
        - Docker Pipeline
        - Docker plugin
        - Blue Ocean
        - Kubernetes plugin
        - Groovy
    
    click Download now and install after restart, wait until finish
