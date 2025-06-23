# Monitor Node Synchronization

## Monitoring Validator Nodes

You can follow the steps below to Monitor your Validator Node.

### Monitor the height of the committed blockchain

Immediately after starting your node, run the following command to identify the block that your node is resuming from:

```
$ tail -f <logs_root>/supra.log | grep 'Resuming at'
```

After a few seconds (or up to a couple of minutes if the database is very large) a log entry should output that includes information about the last committed block present in the node's database. Take note of the height of this block.

After recording this height, run the following command to monitor the synchronization process:

```
$ tail -f <logs_root>/supra.log  | grep -i -e "Processing new Commit QC" -e "Successfully finalized" -e "Committing"
```

You should initially see mostly `Successfully finalized` messages as your node syncs missing blocks from its peers. Subsequently, they should mostly see `Processing new Commit QC` messages as your node syncs the corresponding certificates. Once the height of the `Processing new Commit QC` entry reaches the height of the last committed block, you should start to see Committing logs. This sequence may repeat a few times before the node finally catches up to the head of the chain, which you can tell by comparing the height of the Committing logs to the heights reported by SupraScan.

We realise that this process is quite complicated and are working on creating a script to automate it.

The output of the above log should look similar to the below screenshot when your node is in sync. Note the increasing heights.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfoGU1w6tYhbFwRUTV1L2BaQVEzpyGUALhDpL_AJsXAld11Ld6opK5kWVED9hKl10RHHfnxgetij9Y84RwgfSGiN8UmgirsUNVNMtILrSZ1sx_uZT_C1tn87ia1DNSmpd6VVMpabA?key=77OlmW1ix_9-tCfL7TkgI1Vz)

### Check if your node is proposing new blocks

Your node should start proposing as soon as it enters the current consensus epoch. There are two different commands you can use to check if your node is proposing:

```
$ tail -f <logs_root>/supra.log | grep "Proposing"
```

and

```
$ grep "Proposing" <logs_root>/*
```

The first command will pick up new proposal log entries as they are written, so may take some time to produce any output, and will only produce output if your node is currently proposing. This command will need to be restarted each time the log is rotated, so please check the timestamps of the log files included in `<logs_root>`to get an estimate for how frequently you will need to restart the command.&#x20;

The second command will show all proposal records that are contained in your node's logs. Check the timestamps of these entries (if there are any entries), to see if your node has proposed recently.&#x20;

## Monitoring RPC Nodes

For Monitoring RPC Nodes,&#x20;

You can run the following command:

```
tail -f smr_node_logs/rpc_node.log | grep 'Block height'
```

And then ensure that:

1. &#x20;Output is shown
2. &#x20; The block heights reported in the log entries are increasing.

\
\


\
\
