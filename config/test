import discord
import asyncio
from discord.voice_client import VoiceClient
from discord.ext.commands import Bot
from discord.ext import commands
Client = discord.Client()
bot_prefix= "dev."
client = commands.Bot(command_prefix=bot_prefix)
vc_clients = {}

@client.event
async def on_ready():
    global playingsong, queue
    print("Bot Online!")
    print("Name: {}".format(client.user.name))
    print("ID: {}".format(client.user.id))
    await client.change_presence(game=discord.Game(name='type .help'))
    playingsong = False
    queue = []
 
@client.command(pass_context=True)
async def help(ctx):
    await client.say("This is a help message")
   
 
#Allows the bot to disconnect:
@client.command(pass_context = True)
async def disconnect(ctx):
    global playingsong
    player = vc_clients[ctx.message.server.id][1]
    player.stop()
    playingsong = False
    if ctx.message.server.id not in vc_clients:
        return await client.say("I am not in any voice channel!")

    try:
        vc_clients[ctx.message.server.id][1].stop()
    except:
        pass

    await vc_clients[ctx.message.server.id][0].disconnect()
    return await client.say("Disconnected from voice channel!")

    
#Allows the bot to play music:
@client.command(pass_context = True)
async def play(ctx, url):
    global queue
    global playingsong
    message = ctx.message
    print(url);
    if ctx.message.server.id in vc_clients:
        try:
            vc = await client.join_voice_channel(message.author.voice_channel) 
            vc_clients[message.server.id] = [vc]
        except Exception as e:
            print(e)
            
    if ctx.message.server.id not in vc_clients:
        try:
            vc = await client.join_voice_channel(message.author.voice_channel) 
            vc_clients[message.server.id] = [vc]

        except Exception as e:
            print(e)
            return await client.say("You are not in any voice channel.")
 
    try:
        
        mg = await client.say("Loading...")
        player = await vc_clients[ctx.message.server.id][0].create_ytdl_player(url)
        if (player.is_playing() == False):
            playingsong = False
        if (playingsong == True):
            await client.say("Added to queue")
            queue.append(url)
            print(queue)
        else:
            vc_clients[message.server.id].append(player)
            player.start()
            playingsong = True
            embed = discord.Embed(title = "Now playing :musical_note:", description = str(player.title), color = 0x0000FF)
            embed.add_field(name = "Duration (in seconds)", value = str(player.duration))
            embed.add_field(name = "Requested by", value = str(message.author))
            await client.delete_message(mg)
            await client.say(embed = embed)
            while True:
                if (playingsong == True):
                    continue
                else:
                    x = 1
                    player = await vc_clients[ctx.message.server.id][0].create_ytdl_player(queue[x])
                    vc_clients[message.server.id].append(player)
                    player.start()
                    playingsong = True
                    embed = discord.Embed(title = "Now playing :musical_note:", description = str(player.title), color = 0x0000FF)
                    embed.add_field(name = "Duration (in seconds)", value = str(player.duration))
                    embed.add_field(name = "Requested by", value = str(message.author))
                    await client.delete_message(mg)
                    await client.say(embed = embed)
                    x = x+1

    except Exception as e:
        return await client.say("ERROR: `%s"%e)

#Allows the bot to clear chat (up to fourteen days.)
@client.command(pass_context = True)
async def clear(ctx, number):
    mgs = []
    number = int(number) #Converting the amount of messages to delete to an integer
    async for x in client.logs_from(ctx.message.channel, limit = number):
        mgs.append(x)
    await client.delete_messages(mgs)
    await client.say(str(number) + " messages cleared!")
 
#Gets a list of all banned users on the server:
@client.command(pass_context = True)
async def getbans(ctx):
    x = await client.get_bans(ctx.message.server)
    x = '\n'.join([y.name for y in x])
    embed = discord.Embed(title = "List of Banned Members", description = x, color = 0xFFFFF)
    return await client.say(embed = embed)
