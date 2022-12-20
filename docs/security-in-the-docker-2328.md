# 码头上的保安

> 原文：<https://dev.to/kavyasahu/security-in-the-docker-2328>

> > The safety of dockers is very important. This is because it is used in a production environment. If its security is not enhanced, private data and information may be lost and fall into the hands of others.
> > 
> > The first measure to ensure the security of docker is to use the "Docker" group. If you don't know how to do this, please consult "Docker". Kevin Watts' First Sight. Users added to this group are free to access computers and perform any tasks, including modifying file systems. This explains why you need to be careful when adding users to a group. Only trusted users should be added to the group.

了解如何使用 Docker，从初级基础到高级技术，由行业专家讲授在线视频教程。免费报名[码头工人培训](https://mindmajix.com/docker-training)演示！

> > In addition, Docker introduced the tag "–-security-opt" in the command line. With this flag, users will be able to set AppArmor and SELinux profiles and labels. Suppose you propose a policy that allows containers to only listen on Apache ports. If this policy is defined in svirt_apache, it can be applied to your container by using the following command:
> > 
> > This will make the process of users running docker-in-docker very easy, because they don't have to use "docker run-privileged" on the above kernel.