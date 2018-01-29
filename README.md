# bulk-ssh-using-tmux
Just a brief description of how I use tmux to ssh into multiple ec2 instances running on AWS ASG from my Mac

1. Install homebrew from https://brew.sh/
2. `brew install fish`
3. `brew install jq`
4. `brew install tmux`
5. `gem install tmuxinator`
6. Create the below tmuxinator file
```
# ~/.tmuxinator/dynamic-ssh.yml

name: dynamic-ssh
root: ~

windows:
  - win1:
      layout: tiled
      panes:
        <% @args.each_with_index do |machine, idx|  %>
        - pane<%= idx + 1 %>:
          - ssh <your-ssh-user>@<%= machine %>
        <% end %>
```

If you frequently connect to new hosts and want to avoid specifying yes everytime, you can add `-o StrictHostKeyChecking=no` to your ssh command. More info [here](https://unix.stackexchange.com/questions/33271/how-to-avoid-ssh-asking-permission)

7. Create the below fish function
```
# ~/.config/fish/functions/launch.fish
function launch
  tmuxinator start dynamic-ssh ( curl "{API-URL-to-get-JSON-array-of-ec2-instance-name}" | jq -r 'join("\n" )' )
end
```
