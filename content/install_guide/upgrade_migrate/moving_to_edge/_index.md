+++
title = "Eucalyptus Migration to Edge Networking Mode"
weight = 10
+++

You can configure your existing cloud to use Edge networking mode. This topic provides instructions for configuring and installing additional Eucalyptus components in an existing environment for the purpose of moving to Edge.
{{% notice note %}}
Migrating to Edge will require downtime of your cloud platform. 
{{% /notice %}}
Terminate all running instances. Find out which instances are running: 
    euca-describe-instances

List the instances to terminate: 
    euca-terminate-instances instance_id [instance_id ...]

Shut down all Eucalyptus services. For more information, see [](upgrade_shutdown.dita) . 
    systemctl stop eucalyptus-cloud.service

Edit all the config files on NC and CC for Edge networking mode. For more information, see [](nw_edge.dita) . Install `eucanetd` on all NCs. 
    yum install eucanetd

Start `eucanetd` on all NCs 
    systemctl start eucanetd.service

Start all Eucalyptus services: CLC, CC, WS, SC, NCs. For more information, see [](starting_euca.dita) . Set the Edge JSON property. For more information, see [](nw_edge.dita#nw_edge_json) . Your Edge networking mode is now properly configured. 