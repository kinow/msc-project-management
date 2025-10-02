# Jupyter Lab through SSH Tunnel

The slides of the professor told us to use ``jupyter lab --ip `hostname -i``` to launch the
Jupyter Lan instance inside a compute node, and then access the IP address displayed
in a browser, making sure we were connected to the VPN.

But for some reason my laptop in Barcelona is not able to access the compute nodes.

I can access CESGA FT3 without issues.

I don't have any other VPN active, and I tried disabling my firewall (`ufw` and `iptables`)
and from different browsers. `tcpdump -i tunsnx` shows traffic flowing without issues
through the `snx` VPN, except when I type the compute node IP.

Looking at `route -v`, I think the IP address and mask for the compute node are in
my route table. But perhaps there's a firewall or some other networking tool filtering
connections from outside -- or from Barcelona 🙂

I also found [other CESGA technical documentation](https://cesga-docs.gitlab.io/bigdata-user-guide/jupyter.html)
explaining to users how to use the `start_jupyter` utility in order to launch
a Jupyter Lab, and with a Warning for users to remember to use the VPN to access it.

Alas, none of the above worked for me.

## SSH Tunnel

This worked; in one terminal:

```bash
$ ssh ft3.cesga.es
$ compute --gpu
$ ...
$ jupyter lab --ip `hostname -i`
```

Take note of the compute node (e.g. `c206-3`) and the IP displayed in Jupyter lab.

Then, in another terminal:

```bash
$ ssh -L 8888:10.120.???.???:8888 -J ft3.cesga.es curso???@c206-3
```

Now you should have access to the Jupyter Lab notebooks via <http://localhost:8888/>.
