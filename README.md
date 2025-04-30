import telebot
import feedparser
import random
import requests
import json


TOKEN_TELEGRAM = "7160544412:AAGCvFIJAB9lggM8zL90D-bvGtcmyjbEkeg"

TOKEN_SPORTSDATA = "81294598f0b84778a779cb4c600b9583"

bot = telebot.TeleBot(TOKEN_TELEGRAM)

@bot.message_handler(commands=['start', 'oi', 'fala' , 'eai', 'Oi' , 'Ola' , 'ola'])
def cumprimentar(mensagem):
    texto = """
    E aí, Furioso ou Furiosa! ⚫️⚪️ Que bom ter você por aqui!
    Como posso te ajudar hoje? 😉

    Você pode usar os seguintes comandos:

    /noticias - Para receber as últimas notícias sobre o time de CS.
    /proximosjogos - Para saber as datas e horários dos próximos jogos.
    /hino - Para cantar junto o hino da FURIA!
    /aleatorio - Uma mensagem aleatória pra você!
    /site - Entre no nosso site e confira todos os nossos produtos.
    /redes - Para acessar nossas redes sociais.
    /jogadores - Para ver a lista dos jogadores titulares da FURIA.
    /competicoes - Para ver as competições de CS:GO.
    """
    bot.reply_to(mensagem, texto)

@bot.message_handler(commands=['noticias'])
def mostrar_noticias(mensagem):
    url_api_noticias = 'https://hltv-api.vercel.app/api/news.json'
    try:
        response = requests.get(url_api_noticias)
        response.raise_for_status()
        noticias = response.json()
        if noticias:
            texto = "ÚLTIMAS NOTÍCIAS DA FURIA (HLTV):\n\n"
            for noticia in noticias[:5]:
                texto += f"📰 <a href='{noticia['link']}'>{noticia['title']}</a>\n"
        else:
            texto = "Nenhuma notícia recente encontrada no momento."
    except requests.exceptions.RequestException as e:
        texto = f"Erro ao buscar notícias: {e}"
    bot.reply_to(mensagem, texto, parse_mode='HTML')

@bot.message_handler(commands=['proximosjogos'])
def mostrar_proximos_jogos(mensagem):
    jogos = [
        "🗓️ 10/05 - ESTREIA Pgl Astana 2025 - sem jogo definido ate o momento.",
        "🗓️ 19/05 - ESTREIA IEM Dallas 2025 - sem jogo definido ate o momento.",
        "🗓️ 07/06 - ESTREIA Blast Austin Major - sem jogo definido ate o momento."
    ]
    texto = "PRÓXIMOS JOGOS:\n\n" + "\n".join(f"🗓️ {jogo}" for jogo in jogos)
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
    texto = random.choice(mensagens)
    bot.reply_to(mensagem, texto)

@bot.message_handler(commands=['site'])
def mostrar_site(mensagem):
    site = "https://www.furia.gg/"
    bot.reply_to(mensagem, site)

@bot.message_handler(commands=['redes'])
def mostrar_redes(mensagem):
    redes = [
        "Instagram: https://www.instagram.com/furiagg/?hl=pt-br",
        "Twitter: https://x.com/FURIA",
        "TikTok: https://www.tiktok.com/@furia",
        "YouTube: https://www.youtube.com/channel/UCE4elIT7DqDv545IA71feHg?sub_confirmation=1",
        "Twitch: https://www.twitch.tv/team/furia"
    ]
    texto = "Nossas redes sociais:\n\n" + "\n".join(redes)
    bot.reply_to(mensagem, texto)

def buscar_jogadores_furia():
    url = f"https://api.sportsdata.io/v3/csgo/scores/json/MembershipsByTeam/100000205?key={TOKEN_SPORTSDATA}"
    try:
        response = requests.get(url)
        response.raise_for_status()
        jogadores = response.json()
        print(json.dumps(jogadores, indent=4))
        if jogadores:
            mensagem_jogadores = "Jogadores da FURIA:\n"
            for jogador_data in jogadores:
                player_name = jogador_data.get('PlayerName', 'N/A')
                team_area = jogador_data.get('TeamArea', 'N/A')
                nicknames = {
                    "Yuri Santos": "Yuurih",
                    "Kaike Cerato": "Kscerato",
                    "Gabriel Toledo": "FalleN",
                    "Marcelo Cespedes": "chelo",
                    "Kayke Bertolucci": "kye",
                    "Felipe Medeiros": "skullz",
                    "Danil Golubenko": "molodoy"
                
                }
                nick = nicknames.get(player_name, "")
                if nick:
                    mensagem_jogadores += f"- {player_name} \"{nick}\" ({team_area})\n"
                else:
                    mensagem_jogadores += f"- {player_name} ({team_area})\n"
            return mensagem_jogadores
        else:
            return "Não foram encontrados jogadores da FURIA."
    except requests.exceptions.RequestException as e:
        return f"Erro ao buscar jogadores: {e}"

@bot.message_handler(commands=['jogadores'])
def mostrar_jogadores(mensagem):
    texto = buscar_jogadores_furia()
    bot.reply_to(mensagem, texto)

def buscar_competicoes_csgo():
    url = f"https://api.sportsdata.io/v3/csgo/scores/json/Competitions?key={TOKEN_SPORTSDATA}"
    try:
        response = requests.get(url)
        response.raise_for_status()
        competicoes = response.json()
        if competicoes:
            mensagem = "Competições de CS:GO (Temporada Mais Recente):\n"
            for competicao in competicoes:
                competition_name = competicao.get('Name', 'N/A')
                start_date = 'N/A'
                end_date = 'N/A'
                if competicao.get('Seasons'):
                    ultima_temporada = competicao['Seasons'][-1]
                    start_date = ultima_temporada.get('StartDate', 'N/A')[:10]
                    end_date = ultima_temporada.get('EndDate', 'N/A')[:10]

                mensagem += f"- {competition_name} ({start_date} - {end_date})\n"
            return mensagem
        else:
            return "Não foram encontradas competições de CS:GO."
    except requests.exceptions.RequestException as e:
        return f"Erro ao buscar competições: {e}"

@bot.message_handler(commands=['competicoes'])
def mostrar_competicoes(mensagem):
    texto = buscar_competicoes_csgo()
    bot.reply_to(mensagem, texto)

@bot.message_handler(func=lambda message: True)
def responder_outras_mensagens(mensagem):
    texto = "Desculpe, não entendi o que você quis dizer. Use os comandos / para ver o que posso fazer."
    bot.reply_to(mensagem, texto)

bot.polling()
