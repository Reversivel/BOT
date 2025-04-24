import telebot


TOKEN = "API"

bot = telebot.TeleBot(TOKEN)

@bot.message_handler(commands=['start', 'oi', 'fala'])
def cumprimentar(mensagem):
    texto = """
    E aí, Furioso ou Furiosa! ⚫️⚪️ Que bom ter você por aqui!
    Como posso te ajudar hoje? 😉

    Você pode usar os seguintes comandos:

    /noticias - Para receber as últimas notícias sobre o time de CS.
    /proximosjogos - Para saber as datas e horários dos próximos jogos.
    /elenco - Para conhecer os jogadores do time.
    /hino - Para cantar junto o hino da FURIA!
    /aleatorio - Uma mensagem aleatória pra você!
    /site - Entre no nosso site e confira tudos os nossos produtos.
    """
    bot.reply_to(mensagem, texto)

@bot.message_handler(commands=['noticias'])
def mostrar_noticias(mensagem):
    # *** Aqui você buscaria as notícias mais recentes da FURIA (de um site, API, etc.) ***
    noticias = [
        "FURIA avança nas contratações!",
        "Yekindar é contratado para reforçar o time.",
        "Próximo jogo será em Astana."
    ]
    texto = "ÚLTIMAS NOTÍCIAS DA FURIA:\n\n" + "\n".join(f"🔥 {noticia}" for noticia in noticias)
    bot.reply_to(mensagem, texto)

@bot.message_handler(commands=['proximosjogos'])
def mostrar_proximos_jogos(mensagem):
    # *** Aqui você buscaria a agenda dos próximos jogos ***
    jogos = [
        "10/05 - ESTREIA Pgl Astana 2025 - sem jogo definido ate o momento.",
        "19/05 - ESTREIA IEM Dallas 2025 - sem jogo definido ate o momento.",
        "07/06 - ESTREIA Blast Austin Major - sem jogo definido ate o momento."
    ]
    texto = "PRÓXIMOS JOGOS:\n\n" + "\n".join(f"🗓️ {jogo}" for jogo in jogos)
    bot.reply_to(mensagem, texto)

@bot.message_handler(commands=['elenco'])
def mostrar_elenco(mensagem):
    # *** Aqui você listaria os jogadores do time com suas posições ***
    elenco = {
        "KSCERATO": "Rifler",
        "yuurih": "Rifler",
        "Fallen": "IGL/Rifler",
        "YEKINDAR": "Rifler",
        "molodoy": "AWP"
    }
    texto = "ELENCO ATUAL DA FURIA:\n\n" + "\n".join(f"👤 {jogador}: {posicao}" for jogador, posicao in elenco.items())
    bot.reply_to(mensagem, texto)

@bot.message_handler(commands=['hino'])
def mostrar_hino(mensagem):
    hino = """
    Oh, FURIA, meu orgulho, minha paixão!
    Em cada round, a garra e o coração.
    Com a garra de um leão, a força do trovão,
    A vitória é o nosso destino, campeão!
    ⚫️⚪️⚫️⚪️⚫️⚪️
    """
    bot.reply_to(mensagem, hino)

@bot.message_handler(commands=['aleatorio'])
def mensagem_aleatoria(mensagem):
    mensagens = [
        "VAMOS, FURIA!",
        "A pantera vai rugir!",
        "No clutch ou no rush, a vitória é nossa!",
        "Sinta a pressão, o jogo é nosso!",
        "Respeita a FURIA!"
    ]
    import random
    texto = random.choice(mensagens)
    bot.reply_to(mensagem, texto)

@bot.message_handler(commands=['site'])
def mensagem_aleatoria(mensagem):
    site = [
        "https://www.furia.gg/"
    ]
    bot.reply_to(mensagem, site)

@bot.message_handler(func=lambda message: True)
def responder_outras_mensagens(mensagem):
    texto = "Desculpe, não entendi o que você quis dizer. Use os comandos / para ver o que posso fazer."
    bot.reply_to(mensagem, texto)

bot.polling()
