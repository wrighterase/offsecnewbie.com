# Pre-engagement

### Log all commands of the current session

```text
script $target.log
....
commands and output of commands you ran in that 1 terminal sesssion
....
exit # when finished
```

#### Use Cherrytree or OneNote other to document findings...even a text file!

#### Create a screenshot of the selected area and save it at home directory

```text
shift Print Screen
```

### Set the Target IP Address to the $ip system variable

```text
export ip=target_ip
```

{% hint style="success" %}
If you're working on a single target it is useful to do the `export ip=target_ip` command before you run Tmux. That way when you create new tabs in Tmux you don't have to run the export command for every new tab.
{% endhint %}

{% page-ref page="kali-configuration/tmux-config.md" %}

{% page-ref page="kali-configuration/terminator-shortcuts.md" %}

