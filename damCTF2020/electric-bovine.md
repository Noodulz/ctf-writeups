I'd just like to first say, as someone who's already built 2 Discord bots, this challenge really peaked my interest and was also about the only thing that tore me away from sleeping in the rest of this Saturday afternoon.

So my first instinct in enumerating and exploring the internals was the source code itself. When skimming through, you can see here that upon joining the bot server, new members automatically get assigned with a basic role:

```
role=client.guilds[0].get_role(763128055429595156)
awaitmember.add_roles(role)
```
I played around with the commands listed in the help command. When scouring through the links with `!resource`, all 4 videos happened to be cow-related memes on long hour loops, which lead me to thinking that the flag must have something to do with the `!cowsay` command. 

However, upon triggering `!cowsay`, the bot sent back a denial due to permissions. So I must've had to somehow add a certain role for me to be able to trigger `!cowsay`. When looking at the `!cowsay` function, I found that it checked the user's role IDs to see if it was equal to or larger than the specified ID the `!cowsay` function was looking for:
```
else:
      if message.author == client.user:
                return
      elif(client.guilds[0].get_member(message.author.id).guild_permissions >= client.guilds[0].get_role(763128087226351638).permissions):
                # accept, do cowsay.
                try:
                    arg = message.content.split("!cowsay ")[1]
```
So I tried adding the role with `!role_add`. However, I had to be in the server's channels to send that command. But when looking at the bot server, I was not allowed to send *any* messages. Weird. 

Then after a while of looking, I finally noticed that `!send_msg` could send anything to the #botspam channel that was hidden in the bot server. So I try injecting a command with `!send_msg !role_add @username <@!763128087226351638>` and make a dent! However, I get this message back:
```
Hmmm... Noodles wants to add role private. Interesting. . .
Denied role private to member Noodles. Gotta hack harder than that!
```
Darn it. Looking back at the first couple lines I thought the bot didn't parse all of the numbers in the ID so I'd tried padding it with extra repeats on both ends but, nada. 

Then eventually with some help from my other teammates, I'd tried experimenting with my nickname in the server and changing it to private, just like the role I tried to request. And boom! I get this back:
```
Hmmm... Noodles wants to add role private. Interesting. . .
Granted role private to member Noodles. Well Done!
```
Interesting indeed...

So now I could screw around with the cowsay command. However, when trying the obvious `!cowsay cat flag.txt`, I get back an invalid character message. Turns out, in the source code, that no special characters were allowed nor even whitespaces. Darn. Obviously this was a command injection I had to complete with the `!cowsay` command, so how else could I bypass this?

Well! After testing out some other special characters like #, $, ;{}, and eventually <, I make yet another dent with <. When I send < to the `!cowsay` command, the bot returns a blank, and not the typical cow output I'd expected. So `<` must be something!

Eventually I learn that I could filter through flag.txt to pipe it through and read, and boom, I finally get the flag with `!cowsay <flag`, which outputted this:
```
 __________________________
< dam{discord_su_do_speen} >
 --------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```
Extremely fun challenge overall! I hope to play more interesting challs such as this in the future. Also much thanks to my teammates at Kernel Sanders. Really appreciate them. 
