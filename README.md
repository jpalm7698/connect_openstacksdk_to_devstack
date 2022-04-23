# Connect OpenStackSDK to your local DevStack cloud
- A quick guide that provides context and references behind each step.
- The current release of OpenStack at the time of writing is Yoga. 
- This guide and its reference links may become outdated when the next major version of OpenStack is released.

1. [Install DevStack to your Linux System if you haven't already.](https://docs.openstack.org/DevStack/latest/)

1. Ensure your DevStack cloud is installed properly and its services are running:
    ```
    sudo systemctl status "devstack@*"
    ```

1. Create the [clouds.yaml](https://docs.openstack.org/python-openstackclient/yoga/configuration/index.html#clouds-yaml) configuration file for `openstacksdk`:
    ```
    cd ~ # your normal user account you use, not the user account with the DevStack environment installed
    mkdir .config
    touch clouds.yaml
    ```
    This file provides the list of OpenStack clouds the `openstack` module (within `openstacksdk`) can connect to.

1. Paste the following [config](https://docs.openstack.org/openstacksdk/latest/user/guides/connect_from_config.html#default-location) info into 'clouds.yaml'. Update the configuration based on your DevStack login credentials:
    ``` 
    clouds:
        devstack:
            auth:
            auth_url: http://127.0.0.1/identity 	# 127.0.0.1 - i.e., localhost
            password: password 			# replace with $OS_PASSWORD; defined in 'local.conf' within your local DevStack repository
            project_domain_id: default
            project_name: demo			# replace with $OS_PROJECT_NAME; default is 'demo'
            user_domain_id: default
            username: demo				# replace with $OS_USERNAME; default is 'demo'
    ```

    View [openrc](https://docs.openstack.org/devstack/latest/configuration.html#openrc) to learn more about how your DevStack login credentials are determined.

1. [Connect to your DevStack cloud using the openstack module in openstacksdk](https://opendev.org/openstack/openstacksdk/src/branch/master/README.rst):

    In your python3 environment with `openstacksdk` installed:
    ```
    import openstack
    openstack.enable_logging(debug=True) # will show HTTP request info when we get the list of servers
    conn = openstack.connect(cloud='devstack')
    for server in conn.compute.servers(): # using proxy layer - 'compute.servers'
        # will return None if you haven't setup a server (i.e., you just setup your DevStack environment and haven't messed with it)
        # regardless, debug logging will be show up if enabled
        print(server.to_dict()) 
    ```

    If you don't get an error from the openstacksdk package (e.g., `keystoneauth1.exceptions.http.Unauthorized`) then you should be good to go!