## This guide is for admins to troubleshoot seamicro chassis when needed

### node operations
`ssh into admin@<chassis>`

#### See summary/status
`show server summary n/0`

#### Power Off
`enable; power-off server n/0 [force]`

#### Power on
`enable; power-on server n/0 [force]`

#### reset a node
`enable; reset server n/0`

### Chassis operation
`ssh into admin@<chassis>`

#### Power off all nodes
`enable; power-off server all [force]`

#### Power on all nodes
`enable; power-on server all [force]`

#### Reset chassis
```
enable; power-off server all [force]
reload
```

### Access serial console of a seamicro node
```
ssh into admin@<chassis>
enable; server console connect n/0
```

Note: seamicro nodes start from 0 whereas they are marked from 1 in duffy db.
So, say to poweroff n6.pufty, we would do `enable; power-off server 5/0`

To see all commands and detailed usage, please check the seamicro docs in this directory
of repo
