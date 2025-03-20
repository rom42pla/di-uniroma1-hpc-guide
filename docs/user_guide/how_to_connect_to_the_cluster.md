# How to connect to the cluster

Connecting to the cluster involves several steps.

1. Connect to the `frontend` server. You can do this using `ssh` to the address `151.100.174.45` which is accessible only through Sapienza network or its [VPN](https://sites.google.com/di.uniroma1.it/cluster/home-page):
    ```
    $ ssh <user>@151.100.174.45
    <user>@151.100.174.45's password:
    ```
    Make sure to replace `<user>` with your username. 
    If you don't have one, [ask an admin](Home.md) to create one for you.
1. Once you're in the front-end node, you have to `ssh` again into the `submitter` node, from which you can use Slurm:
    ```
    $ ssh 192.168.0.2
    <user>@192.168.0.2's password:
    ```
    Once there, you can [use Slurm](how_to_use_slurm.md).

