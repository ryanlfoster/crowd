# Atlassian Crowd

A single sign-on and user identity tool that's easy to use, administer, and integrate.

Users can come from anywhere: Active Directory, LDAP, Crowd itself, or any mix thereof. Control permissions to all your applications in one place – Atlassian, Subversion, Google Apps, or your own apps.

This is inspired by [Hbokh/docker-jira-postgresql](https://github.com/hbokh/docker-jira-postgresql)
but the installation and run process is made in a more generic way, to serve as base
for the installation of other Atlassian Products.

## Instalation

Is best practice to separate the data from the container. This instalation process
will assume this.

### 1. Create a data-only container for crowd

Create a data-only container from Busybox (very small footprint) and name it "crowd\_datastore":

    docker run -v /opt/crowd-home --name=crowd\_datastore -d busybox echo "crowd data"

**NOTE**: data-only containers don't have to run / be active to be used.

### 2. Create a PostgreSQL-container

See: [POSTGRESQL](POSTGRESQL.md)

### 3. Start the Software container

    docker run -d --name crowd -p 8085:8085 --link postgresql:db atende/crowd \
    --volumes-from crowd\_datastore

## Running Behind a Proxy

In production environments is a best practice run the container on port 80 and
use a appropriated name like http://software.company.com

In this cases you could also need to run the software behind a proxy, so you can
run other applications in the same host.

This is a common feature of the atlassian images generetad by Atende Tecnology.

See the [Instructions](RUNNING_PROXY.md)

## Easy Running Everything at Once

Using [Crane](https://github.com/michaelsauter/crane) you can easy startup all containers at once respecting its dependencies. This is by far the easy way to create a environment with several atlassian tools and ready for production. Just install docker, install crane and see [crane.yml](crane.yml) file.
Change the file with your values and create the databases for each application
in the *postgresql* container and configure each application accessing it interface.

If you keep the proxy settings (see [Running behind Proxy](RUNNING_PROXY.md) for details),
it will also automatically create the proxy. Now you just need to change your DNS server

If you are migrating, restore your data in the data containers, and in the database

The startup script for crane command could be something like that:

    /usr/local/bin/crane lift -c /etc/crane.yml > /dev/null 2>&1
