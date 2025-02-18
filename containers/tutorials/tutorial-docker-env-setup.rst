.. _tutorial-docker-env-setup:

Tutorial Docker environment setup
=================================

Introduction
------------

A Neuron application can be deployed using docker containers. This
tutorial describes how to configure docker to expose Inferentia/Trainium devices
to containers.


.. tab-set::

   .. tab-item:: Training

        .. dropdown:: Install Drivers
            :class-title: sphinx-design-class-title-small
            :class-body: sphinx-design-class-body-small
            :animate: fade-in

            .. code:: bash

               # Configure Linux for Neuron repository updates

               sudo tee /etc/yum.repos.d/neuron.repo > /dev/null <<EOF
               [neuron]
               name=Neuron YUM Repository
               baseurl=https://yum.repos.neuron.amazonaws.com
               enabled=1
               metadata_expire=0
               EOF
               sudo rpm --import https://yum.repos.neuron.amazonaws.com/GPG-PUB-KEY-AMAZON-AWS-NEURON.PUB

               # Update OS packages
               sudo yum update -y


               # Install OS headers
               sudo yum install kernel-devel-$(uname -r) kernel-headers-$(uname -r) -y

               # Remove preinstalled packages and Install Neuron Driver and Runtime
               sudo yum remove aws-neuron-dkms -y
               sudo yum remove aws-neuronx-dkms -y
               sudo yum install aws-neuronx-dkms-2.*  -y

               # Install EFA Driver(only required for multiinstance training)
               curl -O https://efa-installer.amazonaws.com/aws-efa-installer-latest.tar.gz
               wget https://efa-installer.amazonaws.com/aws-efa-installer.key && gpg --import aws-efa-installer.key
               cat aws-efa-installer.key | gpg --fingerprint
               wget https://efa-installer.amazonaws.com/aws-efa-installer-latest.tar.gz.sig && gpg --verify ./aws-efa-installer-latest.tar.gz.sig
               tar -xvf aws-efa-installer-latest.tar.gz
               cd aws-efa-installer && sudo bash efa_installer.sh --yes
               cd
               sudo rm -rf aws-efa-installer-latest.tar.gz aws-efa-installer

        .. dropdown:: Install Docker
            :class-title: sphinx-design-class-title-small
            :class-body: sphinx-design-class-body-small
            :animate: fade-in

            .. code:: bash

               sudo yum install -y docker.io
               sudo usermod -aG docker $USER

               Logout and log back in to refresh membership.

        .. dropdown:: Verify Docker
            :class-title: sphinx-design-class-title-small
            :class-body: sphinx-design-class-body-small
            :animate: fade-in

            .. code:: bash

               docker run hello-world

               Expected result:

               ::

                  Hello from Docker!
                  This message shows that your installation appears to be working correctly.

                  To generate this message, Docker took the following steps:
                  1. The Docker client contacted the Docker daemon.
                  2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
                  (amd64)
                  3. The Docker daemon created a new container from that image which runs the
                  executable that produces the output you are currently reading.
                  4. The Docker daemon streamed that output to the Docker client, which sent it
                  to your terminal.

                  To try something more ambitious, you can run an Ubuntu container with:
                  $ docker run -it ubuntu bash

                  Share images, automate workflows, and more with a free Docker ID:
                  https://hub.docker.com/

                  For more examples and ideas, visit:
                  https://docs.docker.com/get-started/

        .. dropdown:: Verify Neuron Component
            :class-title: sphinx-design-class-title-small
            :class-body: sphinx-design-class-body-small
            :animate: fade-in

            Once the environment is setup, a container can be started with
            --device=/dev/neuron# to specify desired set of Inferentia/Trainium devices to be
            exposed to the container. To find out the available neuron devices on
            your instance, use the command ``ls /dev/neuron*``.

            When running neuron-ls inside a container, you will only see the set of
            exposed Trainiums. For example:

            .. code:: bash

               docker run --device=/dev/neuron0 neuron-test neuron-ls

               Would produce the following output in trn1.32xlarge:

               ::

               +--------+--------+--------+---------+
               | NEURON | NEURON | NEURON |   PCI   |
               | DEVICE | CORES  | MEMORY |   BDF   |
               +--------+--------+--------+---------+
               | 0      | 2      | 32 GB  | 10:1c.0 |
               +--------+--------+--------+---------+

   .. tab-item:: Inference

      .. dropdown:: Install Drivers
         :class-title: sphinx-design-class-title-small
         :class-body: sphinx-design-class-body-small
         :animate: fade-in

         .. code:: bash

			# Configure Linux for Neuron repository updates
			sudo tee /etc/yum.repos.d/neuron.repo > /dev/null <<EOF
			[neuron]
			name=Neuron YUM Repository
			baseurl=https://yum.repos.neuron.amazonaws.com
			enabled=1
			metadata_expire=0
			EOF
			sudo rpm --import https://yum.repos.neuron.amazonaws.com/GPG-PUB-KEY-AMAZON-AWS-NEURON.PUB

			# Update OS packages
			sudo yum update -y

			################################################################################################################
			# To install or update to Neuron versions 1.19.1 and newer from previous releases:
			# - DO NOT skip 'aws-neuron-dkms' install or upgrade step, you MUST install or upgrade to latest Neuron driver
			################################################################################################################

			# Install OS headers
			sudo yum install kernel-devel-$(uname -r) kernel-headers-$(uname -r) -y

			# Install Neuron Driver
			sudo yum install aws-neuron-dkms -y

			####################################################################################
			# Warning: If Linux kernel is updated as a result of OS package update
			#          Neuron driver (aws-neuron-dkms) should be re-installed after reboot
			####################################################################################

      .. dropdown:: Install Docker
         :class-title: sphinx-design-class-title-small
         :class-body: sphinx-design-class-body-small
         :animate: fade-in

         .. code:: bash

            sudo yum install -y docker.io
            sudo usermod -aG docker $USER

            Logout and log back in to refresh membership.

      .. dropdown:: Verify Docker
         :class-title: sphinx-design-class-title-small
         :class-body: sphinx-design-class-body-small
         :animate: fade-in

         .. code:: bash

            docker run hello-world

            Expected result:

            ::

               Hello from Docker!
               This message shows that your installation appears to be working correctly.

               To generate this message, Docker took the following steps:
               1. The Docker client contacted the Docker daemon.
               2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
               (amd64)
               3. The Docker daemon created a new container from that image which runs the
               executable that produces the output you are currently reading.
               4. The Docker daemon streamed that output to the Docker client, which sent it
               to your terminal.

               To try something more ambitious, you can run an Ubuntu container with:
               $ docker run -it ubuntu bash

               Share images, automate workflows, and more with a free Docker ID:
               https://hub.docker.com/

               For more examples and ideas, visit:
               https://docs.docker.com/get-started/


      .. dropdown:: Verify Neuron Component
         :class-title: sphinx-design-class-title-small
         :class-body: sphinx-design-class-body-small
         :animate: fade-in

         Once the environment is setup, a container can be started with
         --device=/dev/neuron# to specify desired set of Inferentia/Trainium devices to be
         exposed to the container. To find out the available neuron devices on
         your instance, use the command ``ls /dev/neuron*``.

         When running neuron-ls inside a container, you will only see the set of
         exposed Inferentias. For example:

         .. code:: bash

            docker run --device=/dev/neuron0 neuron-test neuron-ls

         Would produce the following output in inf1.xlarge:

            ::

               +--------------+---------+--------+-----------+-----------+------+------+
               |   PCI BDF    | LOGICAL | NEURON |  MEMORY   |  MEMORY   | EAST | WEST |
               |              |   ID    | CORES  | CHANNEL 0 | CHANNEL 1 |      |      |
               +--------------+---------+--------+-----------+-----------+------+------+
               | 0000:00:1f.0 |       0 |      4 | 4096 MB   | 4096 MB   |    0 |    0 |
               +--------------+---------+--------+-----------+-----------+------+------+

