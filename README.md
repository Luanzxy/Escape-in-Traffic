# Escape-in-Traffic



                 Primeiro Commit


import pygame
import sys
import random

# Inicialização do Pygame
pygame.init()
screen = pygame.display.set_mode((800, 800))  # Tela de 800x800
clock = pygame.time.Clock()

# Cores
PRETO = (0, 0, 0)
BRANCO = (255, 255, 255)
VERDE = (0, 255, 0)
VERMELHO = (255, 0, 0)
AZUL = (0, 0, 255)

# Fonte
pygame.font.init()
fonte = pygame.font.Font(None, 48)

# Função para desenhar um botão
def desenhar_botao(texto, x, y, largura, altura, cor_botao=PRETO):
    retangulo = pygame.Rect(x, y, largura, altura)
    pygame.draw.rect(screen, cor_botao, retangulo)
    texto_renderizado = fonte.render(texto, True, BRANCO)
    screen.blit(texto_renderizado, (x + (largura - texto_renderizado.get_width()) // 2,
                                     y + (altura - texto_renderizado.get_height()) // 2))

# Função para ler o recorde do arquivo
def ler_recorde():
    try:
        with open("recorde.txt", "r") as arquivo:
            return int(arquivo.read())
    except FileNotFoundError:
        return 0  
    except ValueError:
        return 0  

# Função para salvar o recorde no arquivo
def salvar_recorde(recorde):
    with open("recorde.txt", "w") as arquivo:
        arquivo.write(str(recorde))

# Função da tela inicial
def tela_inicial():
    botao_carros = pygame.Rect(0, 0, 0, 0)  
    botao_jogar = pygame.Rect(0, 0, 0, 0)

    recorde = ler_recorde()  

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.MOUSEBUTTONDOWN:
                if event.button == 1:  
                    mouse_pos = pygame.mouse.get_pos()
                    if botao_carros.collidepoint(mouse_pos):
                        tela_carros()  
                    elif botao_jogar.collidepoint(mouse_pos):
                        jogo()  

        screen.fill(AZUL)    
        screen.fill(VERMELHO, rect=(400, 0, 400, 800))  

        titulo_renderizado = fonte.render("Escape in Traffic", True, VERDE)
        screen.blit(titulo_renderizado, (395 - titulo_renderizado.get_width() // 2, 50))

        largura_botao = 200   
        altura_botao = 75
        espaco_entre_botao = 20

        total_width = 2 * largura_botao + espaco_entre_botao
        start_x = (800 - total_width) // 2

        pos_x_carros = start_x
        pos_x_jogar = start_x + largura_botao + espaco_entre_botao

        # Botão "Carros" na esquerda
        botao_carros = pygame.Rect(pos_x_carros, 250, largura_botao, altura_botao)
        desenhar_botao("Carros", botao_carros.x, botao_carros.y, largura_botao, altura_botao)

        # Botão "Jogar" na direita
        botao_jogar = pygame.Rect(pos_x_jogar , 250 , largura_botao , altura_botao)
        desenhar_botao("Jogar", botao_jogar.x , botao_jogar.y , largura_botao , altura_botao)

        # Exibir o recorde na tela inicial como texto
        recorde_renderizado = fonte.render(f'Recorde: {recorde}', True, BRANCO)
        screen.blit(recorde_renderizado,(400 - recorde_renderizado.get_width() // 2 ,350))

        # Atualizar a tela
        pygame.display.flip()
        clock.tick(60)

# Função da tela de Recorde (removida já que agora é só visualização na tela inicial)
def tela_recorde():
    pass

# Função da tela de Carros
def tela_carros():
    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_ESCAPE:  
                    return

        screen.fill(BRANCO) 
        
        titulo_renderizado = fonte.render("Tela de Carros", True, PRETO)
        screen.blit(titulo_renderizado,(400 - titulo_renderizado.get_width() // 2 ,100))

        # Adicione mais elementos aqui conforme necessário

        pygame.display.flip()
        clock.tick(60)

# Função principal do jogo
def jogo():
    try:
        plano_de_fundo = pygame.image.load('estrada.jpg')
        plano_de_fundo = pygame.transform.scale(plano_de_fundo,(800 ,800))
    except pygame.error:
        print("Erro ao carregar a imagem de fundo.")
        return

    try:
        imagem_original = pygame.image.load('carro_verde.png')
        imagem_original = imagem_original.convert_alpha()
    except pygame.error:
        print("Erro ao carregar a imagem do carro.")
        return

    largura_desejada = int(plano_de_fundo.get_width() * 0.15)   
    proporcao = largura_desejada / imagem_original.get_width()
    altura_desejada = int(imagem_original.get_height() * proporcao)

    imagem_alpha = pygame.transform.scale(imagem_original,(largura_desejada ,altura_desejada))
    
    x = (800 - largura_desejada) // 2   
    y = 600   
    velocidade = 5

    rodando = True
    
    outros_carros = []   
    pontuacao = 0

    while rodando:
        
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_ESCAPE:
                    rodando = False

        
        teclas = pygame.key.get_pressed()

        
        if teclas[pygame.K_LEFT] and x > 0:
            x -= velocidade

        
        if teclas[pygame.K_RIGHT] and x < screen.get_width() - largura_desejada:
            x += velocidade
        
       
       # Adicionar novos carros aleatoriamente à lista se houver espaço na tela.
    if len(outros_carros) < 5:   
           novo_carro_x = random.randint(0 ,screen.get_width() - largura_desejada)
           novo_carro_rect= imagem_alpha.get_rect(topleft=(novo_carro_x,-altura_desejada)) 
           outros_carros.append(novo_carro_rect)   
        
    screen.blit(plano_de_fundo,(0 ,0))
    screen.blit(imagem_alpha,(x,y))
       
    for carro in outros_carros[:]: 
           carro.y += velocidade /2   
           if carro.y > screen.get_height():   
               outros_carros.remove(carro)
               pontuacao +=1  
           else:
               screen.blit(imagem_alpha,(carro.x ,carro.y))   

               # Verifica colisão
               if carro.colliderect(pygame.Rect(x,y,width=largura_desejada,height=altura_desejada)):
                   print("Colisão! Você perdeu!")
                   rodando=False  

    pontuacao_renderizada=fonte.render(f'Pontuação: {pontuacao}',True)
    screen.blit(pontuacao_renderizada,(10 ,10))

       # Atualiza a tela
pygame.display.flip() 
clock.tick(60)

if __name__ == "__main__":
   tela_inicial() 













                         Segundo Commit








import pygame
import sys
import random

# Inicialização do Pygame
pygame.init()
screen = pygame.display.set_mode((800, 800))  # Tela de 800x800
clock = pygame.time.Clock()

# Cores
PRETO = (0, 0, 0)
BRANCO = (255, 255, 255)
VERDE = (0, 255, 0)
VERMELHO = (255, 0, 0)
AZUL = (0, 0, 255)

# Fonte
pygame.font.init()
fonte = pygame.font.Font(None, 48)

# Função para desenhar um botão
def desenhar_botao(texto, x, y, largura, altura, cor_botao=PRETO):
    retangulo = pygame.Rect(x, y, largura, altura)
    pygame.draw.rect(screen, cor_botao, retangulo)
    texto_renderizado = fonte.render(texto, True, BRANCO)
    screen.blit(texto_renderizado, (x + (largura - texto_renderizado.get_width()) // 2,
                                     y + (altura - texto_renderizado.get_height()) // 2))

# Função para ler o recorde do arquivo
def ler_recorde():
    try:
        with open("recorde.txt", "r") as arquivo:
            return int(arquivo.read())
    except FileNotFoundError:
        return 0  
    except ValueError:
        return 0  

# Função para salvar o recorde no arquivo
def salvar_recorde(recorde):
    with open("recorde.txt", "w") as arquivo:
        arquivo.write(str(recorde))

# Função da tela inicial
def tela_inicial():
    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.MOUSEBUTTONDOWN:
                if event.button == 1:  
                    mouse_pos = pygame.mouse.get_pos()
                    if botao_carros.collidepoint(mouse_pos):
                        tela_carros()
                    elif botao_jogar.collidepoint(mouse_pos):
                        return jogo()  # Sai da tela inicial e vai para o jogo

        screen.fill(AZUL)
        screen.fill(VERMELHO, rect=(400, 0, 400, 800))

        titulo_renderizado = fonte.render("Escape in Traffic", True, VERDE)
        screen.blit(titulo_renderizado, (395 - titulo_renderizado.get_width() // 2, 50))

        largura_botao = 200   
        altura_botao = 75
        espaco_entre_botao = 20

        total_width = 2 * largura_botao + espaco_entre_botao
        start_x = (800 - total_width) // 2

        pos_x_carros = start_x
        pos_x_jogar = start_x + largura_botao + espaco_entre_botao

        # Botão "Carros" na esquerda
        botao_carros = pygame.Rect(pos_x_carros, 250, largura_botao, altura_botao)
        desenhar_botao("Carros", botao_carros.x, botao_carros.y, largura_botao, altura_botao)

        # Botão "Jogar" na direita
        botao_jogar = pygame.Rect(pos_x_jogar , 250 , largura_botao , altura_botao)
        desenhar_botao("Jogar", botao_jogar.x , botao_jogar.y , largura_botao , altura_botao)

        recorde_renderizado = fonte.render(f'Recorde: {ler_recorde()}', True, BRANCO)
        screen.blit(recorde_renderizado,(400 - recorde_renderizado.get_width() // 2 ,350))

        pygame.display.flip()
        clock.tick(60)

        # Resto da lógica da tela inicial


# Função da tela de Recorde (removida já que agora é só visualização na tela inicial)
def tela_recorde():
    pass

# Função da tela de Carros
def tela_carros():
    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_ESCAPE:  
                    return

        screen.fill(BRANCO) 
        
        titulo_renderizado = fonte.render("Tela de Carros", True, PRETO)
        screen.blit(titulo_renderizado,(400 - titulo_renderizado.get_width() // 2 ,100))

        # Adicione mais elementos aqui conforme necessário

        pygame.display.flip()
        clock.tick(60)

carros_principais = ['imagens/carro_principal_verde.png','imagens/carro_principal_vermelho.png']

# Função principal do jogo
def jogo():
    try:
        plano_de_fundo = pygame.image.load('iamgens/estrada.jpg')
        plano_de_fundo = pygame.transform.scale(plano_de_fundo,(800 ,800))
    except pygame.error:
        print("Erro ao carregar a imagem de fundo.")
        return

    try:
        imagem_original_verde = pygame.image.load('imagens/carro_verde.png').convert_alpha()
        imagem_original_vermleho = pygame.image.load('imagens/carro_principal_vermelho.png').convert_alpha()
        carros_pjogar = imagem_original_verde + imagem_original_vermleho
    except pygame.error:
        print("Erro ao carregar a imagem do carro.")
        return

    largura_do_carro = int(plano_de_fundo.get_width() * 0.15)   
    proporcao = largura_do_carro / carros_pjogar.get_width()
    altura_do_carro = int(carros_pjogar.get_height() * proporcao)

    imagem_alpha = pygame.transform.scale(carros_pjogar,(largura_do_carro ,altura_do_carro))
    
    x = (800 - largura_do_carro) // 2   
    y = 600   
    velocidade = 5

    rodando = True

    carros_segundarios_pbaixo = ['imagens/carro_segundario_azul_pbaixo.png','imagens/carro_segundario_branco_pbaixo.png']   
    carros_segundarios_pcima = ['imagens/carro_segundario_azul_pcima.png','imagens/carro_segundario_vermelho_pcima.png']
    carros_segundarios = carros_segundarios_pbaixo + carros_segundarios_pcima
    pontuacao = 0
     
    # Tempo que leva de um carro para outro 
    tempo_ultimo_carro = pygame.time.get_ticks()

    while rodando:
        
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_ESCAPE:
                    rodando = False

        
        teclas = pygame.key.get_pressed()

        
        if teclas[pygame.K_LEFT] and x > 0:
            x -= velocidade

        
        if teclas[pygame.K_RIGHT] and x < screen.get_width() - largura_do_carro:
            x += velocidade
        
       
       # Adicionar novos carros aleatoriamente de 5 em 5 segundos
        if len( carros_segundarios) < 5:
           novo_carro_x = random.randint(0 ,screen.get_width() - largura_do_carro)
           novo_carro_rect= imagem_alpha.get_rect(topleft=(novo_carro_x,-altura_do_carro)) 
           carros_segundarios.append(novo_carro_rect)
           tempo_ultimo_carro = pygame.time.get_tickts() # Faz com que reinicie o temporizador

        
    screen.blit(plano_de_fundo,(0 ,0))
    screen.blit(imagem_alpha,(x,y))
       
    for carro in  carros_segundarios[:]: 
           carro.y += velocidade /2   
           if carro.y > screen.get_height():   
                carros_segundarios.remove(carro)
                pontuacao +=1  
           else:
               screen.blit(imagem_alpha,(carro.x ,carro.y))   

               # Verifica colisão
               if carro.colliderect(pygame.Rect(x,y,width=largura_do_carro,height=altura_do_carro)):
                   print("Colisão! Você perdeu!")
                   rodando=False  

    pontuacao_renderizada=fonte.render(f'Pontuação: {pontuacao}',True, BRANCO)
    screen.blit(pontuacao_renderizada,(10 ,10))
       # Atualiza a tela
pygame.display.flip() 
clock.tick(60)

if __name__ == "__main__":
   tela_inicial()
















                     terceiro commit





import pygame
import sys
import random

# Inicialização do Pygame
pygame.init()
screen = pygame.display.set_mode((800, 800))  # Tela de 800x800
clock = pygame.time.Clock()

# Cores
PRETO = (0, 0, 0)
BRANCO = (255, 255, 255)
VERDE = (0, 255, 0)
VERMELHO = (255, 0, 0)
AZUL = (0, 0, 255)

# Fonte
fonte = pygame.font.Font(None, 48)

# Função para desenhar um botão
def desenhar_botao(texto, x, y, largura, altura, cor_botao=PRETO):
    retangulo = pygame.Rect(x, y, largura, altura)
    pygame.draw.rect(screen, cor_botao, retangulo)
    texto_renderizado = fonte.render(texto, True, BRANCO)
    screen.blit(texto_renderizado, (x + (largura - texto_renderizado.get_width()) // 2,
                                     y + (altura - texto_renderizado.get_height()) // 2))
    return retangulo

# Função para ler o recorde do arquivo
def ler_recorde():
    try:
        with open("recorde.txt", "r") as arquivo:
            return int(arquivo.read())
    except (FileNotFoundError, ValueError):
        return 0  

# Função para salvar o recorde no arquivo
def salvar_recorde(recorde):
    with open("recorde.txt", "w") as arquivo:
        arquivo.write(str(recorde))

# Tela inicial
def tela_inicial():
    try:
        plano_de_fundo_tela_inicial = pygame.image.load('Imagens/plano de fundo.jpg')
        plano_de_fundo_tela_inicial = pygame.transform.scale(plano_de_fundo_tela_inicial, (800, 800))
    except pygame.error:
        print("Erro ao carregar a imagem de fundo.")
        return

    largura_botao = 200
    altura_botao = 75
    espaco_entre_botao = 20

    total_width = 2 * largura_botao + espaco_entre_botao
    start_x = (800 - total_width) // 2

    botao_carros = desenhar_botao("Carros", start_x, 250, largura_botao, altura_botao)
    botao_jogar = desenhar_botao("Jogar", start_x + largura_botao + espaco_entre_botao, 250, largura_botao, altura_botao)

    while True:
        screen.blit(plano_de_fundo_tela_inicial, (0, 0))

        titulo_renderizado = fonte.render("Escape in Traffic", True, VERDE)
        screen.blit(titulo_renderizado, (400 - titulo_renderizado.get_width() // 2, 50))

        desenhar_botao("Carros", botao_carros.x, botao_carros.y, largura_botao, altura_botao)
        desenhar_botao("Jogar", botao_jogar.x, botao_jogar.y, largura_botao, altura_botao)

        recorde_renderizado = fonte.render(f'Recorde: {ler_recorde()}', True, BRANCO)
        screen.blit(recorde_renderizado, (400 - recorde_renderizado.get_width() // 2, 350))

        pygame.display.flip()
        clock.tick(60)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.MOUSEBUTTONDOWN:
                if event.button == 1:
                    mouse_pos = pygame.mouse.get_pos()
                    if botao_carros.collidepoint(mouse_pos):
                        tela_carros()
                    elif botao_jogar.collidepoint(mouse_pos):
                        jogo()

# Tela de carros
def tela_carros():
    try:
        plano_de_fundo_tela_carros = pygame.image.load('Imagens/plano de fundo.jpg')
        plano_de_fundo_tela_carros = pygame.transform.scale(plano_de_fundo_tela_carros, (800, 800))
    except pygame.error:
        print("Erro ao carregar a imagem de fundo.")
        return

    while True:
        screen.blit(plano_de_fundo_tela_carros, (0, 0))
        titulo_renderizado = fonte.render("Tela de Carros", True, PRETO)
        screen.blit(titulo_renderizado, (400 - titulo_renderizado.get_width() // 2, 100))

        pygame.display.flip()
        clock.tick(60)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
                return

# Função do jogo
def jogo():
    try:
        plano_de_fundo = pygame.image.load('imagens/estrada.jpg')
        plano_de_fundo = pygame.transform.scale(plano_de_fundo, (800, 800))
    except pygame.error:
        print("Erro ao carregar a imagem de fundo.")
        return

    try:
        carro_jogador = pygame.image.load('imagens/carro_principal_verde.png').convert_alpha()
    except pygame.error:
        print("Erro ao carregar a imagem do carro.")
        return

    largura_do_carro = 60
    altura_do_carro = 100
    carro_jogador = pygame.transform.scale(carro_jogador, (largura_do_carro, altura_do_carro))

    x, y = 370, 600
    velocidade = 5
    carros_inimigos = []
    tempo_ultimo_carro = pygame.time.get_ticks()
    pontuacao = 0
    rodando = True

    while rodando:
        screen.blit(plano_de_fundo, (0, 0))
        screen.blit(carro_jogador, (x, y))

        teclas = pygame.key.get_pressed()
        if teclas[pygame.K_LEFT] and x > 0:
            x -= velocidade
        if teclas[pygame.K_RIGHT] and x < screen.get_width() - largura_do_carro:
            x += velocidade

        if pygame.time.get_ticks() - tempo_ultimo_carro > 2000:
            carro_x = random.randint(0, screen.get_width() - largura_do_carro)
            carro_rect = pygame.Rect(carro_x, -altura_do_carro, largura_do_carro, altura_do_carro)
            carros_inimigos.append(carro_rect)
            tempo_ultimo_carro = pygame.time.get_ticks()

        for carro in carros_inimigos[:]:
            carro.y += velocidade / 2
            if carro.y > screen.get_height():
                carros_inimigos.remove(carro)
                pontuacao += 1
            elif carro.colliderect(pygame.Rect(x, y, largura_do_carro, altura_do_carro)):
                print("Colisão! Você perdeu!")
                rodando = False

            pygame.draw.rect(screen, VERMELHO, carro)

        pygame.display.flip()
        clock.tick(60)

tela_inicial()




    quarto commit


    import pygame
import sys

# Inicializa o Pygame
pygame.init()

# Definições de cores
VERMELHO = (255, 0, 0)
AZUL = (0, 0, 255)
PRETO = (0, 0, 0)
BRANCO = (255, 255, 255)
CINZA = (128, 128, 128)

# Tamanho da tela
LARGURA_TELA = 800
ALTURA_TELA = 600

# Tamanho do carro
LARGURA_CARRO = 50
ALTURA_CARRO = 50

# Velocidade do carro
VELOCIDADE_CARRO = 5

# Velocidade dos obstáculos
VELOCIDADE_OBSTACULO = 3

# Cria a tela
tela = pygame.display.set_mode((LARGURA_TELA, ALTURA_TELA))

# Configura o título da janela
pygame.display.set_caption('Jogo de Carro')

# Posição inicial do carro
x_carro = LARGURA_TELA / 2
y_carro = ALTURA_TELA - ALTURA_CARRO - 20

# Posição dos obstáculos
obstaculos = [
    {'x': 100, 'y': -100},
    {'x': 300, 'y': -200},
    {'x': 500, 'y': -300}
]

# Variáveis para controlar os estados do jogo
tela_inicial = True
jogo_ativo = False
game_over = False
tela_carros = False

# Tamanho dos botões
LARGURA_BOTAO = 150
ALTURA_BOTAO = 50

# Posições dos botões
botao_jogar = pygame.Rect(LARGURA_TELA / 2 + 20, ALTURA_TELA / 2 - ALTURA_BOTAO / 2, LARGURA_BOTAO, ALTURA_BOTAO)
botao_carros = pygame.Rect(LARGURA_TELA / 2 - LARGURA_BOTAO - 20, ALTURA_TELA / 2 - ALTURA_BOTAO / 2, LARGURA_BOTAO, ALTURA_BOTAO)

# Pontuação e recorde
pontuacao = 0
recorde_pontos = 0

# Carrega as imagens de fundo
fundo_inicial = pygame.image.load('imagens/plano_de_fundo.jpg')
fundo_inicial = pygame.transform.scale(fundo_inicial, (LARGURA_TELA, ALTURA_TELA))

fundo_jogo = pygame.image.load('imagens/estrada.jpg')
fundo_jogo = pygame.transform.scale(fundo_jogo, (LARGURA_TELA, ALTURA_TELA))

fundo_carros = pygame.image.load('imagens/plano_de_fundo.jpg')
fundo_carros = pygame.transform.scale(fundo_carros, (LARGURA_TELA, ALTURA_TELA))

# Carrega as imagens dos carros
carro_azul = pygame.image.load('imagens/carro_principal_verde.png')
carro_azul = pygame.transform.scale(carro_azul, (LARGURA_CARRO, ALTURA_CARRO))

carro_vermelho = pygame.image.load('imagens/carro_segundario_azul_pbaixo.png,imagens/carro_segundario_azul_pcima.png,imagens/carro_segundario_branco_pbaixo.png')
carro_vermelho = pygame.transform.scale(carro_vermelho, (LARGURA_CARRO, ALTURA_CARRO))

# Inicializa o relógio para contar o tempo
relogio = pygame.time.Clock()
ultimo_incremento = pygame.time.get_ticks()

# Loop principal do jogo
while True:
    for evento in pygame.event.get():
        if evento.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        if evento.type == pygame.MOUSEBUTTONDOWN and tela_inicial:
            if botao_jogar.collidepoint(evento.pos):
                tela_inicial = False
                jogo_ativo = True
                pontuacao = 0
            elif botao_carros.collidepoint(evento.pos):
                tela_inicial = False
                tela_carros = True
        if evento.type == pygame.KEYDOWN and tela_carros:
            if evento.key == pygame.K_ESCAPE:
                tela_carros = False
                tela_inicial = True
        if evento.type == pygame.KEYDOWN and game_over:
            # Reinicia o jogo quando pressiona 'R'
            if evento.key == pygame.K_r:
                tela_inicial = False
                jogo_ativo = True
                game_over = False
                pontuacao = 0
                x_carro = LARGURA_TELA / 2
                y_carro = ALTURA_TELA - ALTURA_CARRO - 20
                obstaculos = [
                    {'x': 100, 'y': -100},
                    {'x': 300, 'y': -200},
                    {'x': 500, 'y': -300}
                ]

    if jogo_ativo:
        # Movimentação do carro
        teclas = pygame.key.get_pressed()
        if teclas[pygame.K_LEFT] and x_carro > 0:
            x_carro -= VELOCIDADE_CARRO
        if teclas[pygame.K_RIGHT] and x_carro < LARGURA_TELA - LARGURA_CARRO:
            x_carro += VELOCIDADE_CARRO

        # Movimentação dos obstáculos
        for obstaculo in obstaculos:
            obstaculo['y'] += VELOCIDADE_OBSTACULO
            if obstaculo['y'] > ALTURA_TELA:
                obstaculo['y'] = -ALTURA_CARRO
                obstaculo['x'] = (obstaculo['x'] + 100) % LARGURA_TELA

        # Verifica colisão
        for obstaculo in obstaculos:
            if (obstaculo['x'] < x_carro + LARGURA_CARRO and
                obstaculo['x'] + LARGURA_CARRO > x_carro and
                obstaculo['y'] < y_carro + ALTURA_CARRO and
                obstaculo['y'] + ALTURA_CARRO > y_carro):
                jogo_ativo = False
                game_over = True
                if pontuacao > recorde_pontos:
                    recorde_pontos = pontuacao

        # Incrementa a pontuação a cada segundo
        agora = pygame.time.get_ticks()
        if agora - ultimo_incremento >= 1000:  # 1000 milissegundos = 1 segundo
            pontuacao += 1
            ultimo_incremento = agora

    # Desenho da tela
    if tela_inicial:
        tela.blit(fundo_inicial, (0, 0))
        # Exibe tela inicial
        fonte = pygame.font.Font(None, 64)
        texto_titulo = fonte.render("Jogo de Carro", True, BRANCO)
        tela.blit(texto_titulo, (LARGURA_TELA / 2 - texto_titulo.get_width() / 2, ALTURA_TELA / 2 - texto_titulo.get_height() / 2 - 150))

        # Desenha os botões
        pygame.draw.rect(tela, CINZA, botao_jogar)
        pygame.draw.rect(tela, CINZA, botao_carros)

        fonte = pygame.font.Font(None, 24)
        texto_jogar = fonte.render("Jogar", True, BRANCO)
        texto_carros = fonte.render("Carros", True, BRANCO)
        tela.blit(texto_jogar, (botao_jogar.centerx - texto_jogar.get_width() / 2, botao_jogar.centery - texto_jogar.get_height() / 2))
        tela.blit(texto_carros, (botao_carros.centerx - texto_carros.get_width() / 2, botao_carros.centery - texto_carros.get_height() / 2))
    elif jogo_ativo:
        tela.blit(fundo_jogo, (0, 0))
        # Desenho do carro vermelho
        tela.blit(carro_vermelho, (x_carro, y_carro))

        # Desenho dos obstáculos (carros azuis)
        for obstaculo in obstaculos:
            tela.blit(carro_azul, (obstaculo['x'], obstaculo['y']))

        # Exibe pontuação
        fonte = pygame.font.Font(None, 36)
        texto_pontuacao = fonte.render(f"Pontuação: {pontuacao}", True, BRANCO)
        tela.blit(texto_pontuacao, (10, 10))
    elif tela_carros:
        tela.blit(fundo_carros, (0, 0))
        # Exibe tela de carros
        fonte = pygame.font.Font(None, 64)
        texto_carros = fonte.render("Tela de Carros", True, BRANCO)
        tela.blit(texto_carros, (LARGURA_TELA / 2 - texto_carros.get_width() / 2, ALTURA_TELA / 2 - texto_carros.get_height() / 2))
        texto_reiniciar = fonte.render("", True, BRANCO)
        tela.blit(texto_reiniciar, (LARGURA_TELA / 2 - texto_reiniciar.get_width() / 2, ALTURA_TELA / 2 - texto_reiniciar.get_height() / 2 + 100))
    elif game_over:
        tela.blit(fundo_jogo, (0, 0))
        # Exibe mensagem de game over
        fonte = pygame.font.Font(None, 36)
        texto_game_over = fonte.render("Game Over!", True, BRANCO)
        tela.blit(texto_game_over, (LARGURA_TELA / 2 - texto_game_over.get_width() / 2, ALTURA_TELA / 2 - texto_game_over.get_height() / 2 - 50))

        texto_pontuacao = fonte.render(f"Pontuação: {pontuacao}", True, BRANCO)
        tela.blit(texto_pontuacao, (LARGURA_TELA / 2 - texto_pontuacao.get_width() / 2, ALTURA_TELA / 2 - texto_pontuacao.get_height() / 2))

        texto_recorde_pontos = fonte.render(f"Recorde Pontos: {recorde_pontos}", True, BRANCO)
        tela.blit(texto_recorde_pontos, (LARGURA_TELA / 2 - texto_recorde_pontos.get_width() / 2, ALTURA_TELA / 2 - texto_recorde_pontos.get_height() / 2 + 50))

        texto_reiniciar = fonte.render("Pressione 'R' para reiniciar.", True, BRANCO)
        tela.blit(texto_reiniciar, (LARGURA_TELA / 2 - texto_reiniciar.get_width() / 2, ALTURA_TELA / 2 - texto_reiniciar.get_height() / 2 + 100))

    # Atualiza a tela
    pygame.display.flip()

    # Controle de frames por segundo
    relogio.tick(60)










import pygame
import sys
import random

# Inicialização do Pygame
pygame.init()
screen = pygame.display.set_mode((800, 800))  # Tela de 800x800
clock = pygame.time.Clock()

# Cores
PRETO = (0, 0, 0)
BRANCO = (255, 255, 255)
VERDE = (0, 255, 0)
VERMELHO = (255, 0, 0)
AZUL = (0, 0, 255)

# Fonte
fonte = pygame.font.Font(None, 48)

# Função para desenhar um botão
def desenhar_botao(texto, x, y, largura, altura, cor_botao=PRETO):
    retangulo = pygame.Rect(x, y, largura, altura)
    pygame.draw.rect(screen, cor_botao, retangulo)
    texto_renderizado = fonte.render(texto, True, BRANCO)
    screen.blit(texto_renderizado, (x + (largura - texto_renderizado.get_width()) // 2,
                                    y + (altura - texto_renderizado.get_height()) // 2))
    return retangulo

# Função para ler o recorde do arquivo
def ler_recorde():
    try:
        with open("recorde.txt", "r") as arquivo:
            return int(arquivo.read())
    except (FileNotFoundError, ValueError):
        return 0  

# Função para salvar o recorde no arquivo
def salvar_recorde(recorde):
    with open("recorde.txt", "w") as arquivo:
        arquivo.write(str(recorde))

# Tela inicial
def tela_inicial():
    try:
        plano_de_fundo_tela_inicial = pygame.image.load('Imagens/plano de fundo.jpg')
        plano_de_fundo_tela_inicial = pygame.transform.scale(plano_de_fundo_tela_inicial, (800, 800))
    except pygame.error:
        print("Erro ao carregar a imagem de fundo.")
        return

    largura_botao = 200
    altura_botao = 75
    espaco_entre_botao = 20

    total_width = 2 * largura_botao + espaco_entre_botao
    start_x = (800 - total_width) // 2

    botao_carros = desenhar_botao("Carros", start_x, 250, largura_botao, altura_botao)
    botao_jogar = desenhar_botao("Jogar", start_x + largura_botao + espaco_entre_botao, 250, largura_botao, altura_botao)

    tela_de_destino = 0 # 0: continuar na mesma tela, 1: tela carros, 2: tela jogo
    while tela_de_destino == 0:
        screen.blit(plano_de_fundo_tela_inicial, (0, 0))

        titulo_renderizado = fonte.render("Escape in Traffic", True, PRETO)
        screen.blit(titulo_renderizado, (400 - titulo_renderizado.get_width() // 2, 50))

        desenhar_botao("Carros", botao_carros.x, botao_carros.y, largura_botao, altura_botao)
        desenhar_botao("Jogar", botao_jogar.x, botao_jogar.y, largura_botao, altura_botao)

        recorde_renderizado = fonte.render(f'Recorde: {ler_recorde()}', True, BRANCO)
        screen.blit(recorde_renderizado, (400 - recorde_renderizado.get_width() // 2, 350))

        pygame.display.flip()
        clock.tick(60)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.MOUSEBUTTONDOWN:
                if event.button == 1:
                    mouse_pos = pygame.mouse.get_pos()
                    if botao_carros.collidepoint(mouse_pos):
                        tela_de_destino = 1
                    elif botao_jogar.collidepoint(mouse_pos):
                        tela_de_destino = 2
    if tela_de_destino == 1:
        tela_carros()
    elif tela_de_destino == 2:
        jogo()

# Tela de carros
def tela_carros():
    try:
        plano_de_fundo_tela_carros = pygame.image.load('Imagens/plano de fundo.jpg')
        plano_de_fundo_tela_carros = pygame.transform.scale(plano_de_fundo_tela_carros, (800, 800))
    except pygame.error:
        print("Erro ao carregar a imagem de fundo.")
        return

    while True:
        screen.blit(plano_de_fundo_tela_carros, (0, 0))
        titulo_renderizado = fonte.render("Tela de Carros", True, PRETO)
        screen.blit(titulo_renderizado, (400 - titulo_renderizado.get_width() // 2, 100))

        pygame.display.flip()
        clock.tick(60)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
                tela_inicial()

# Função do jogo
def jogo():
    try:
        carro_segundario_branco = pygame.image.load('Imagens/carro_segundario_branco_pbaixo.png').convert_alpha()
        carro_segundario_azul = pygame.image.load('Imagens/carro_segundario_azul_pbaixo.png').convert_alpha()
    except pygame.error:
        print("Erro ao carregar as imagens dos carros secundários.")
        return

        # Dentro do loop do jogo
        screen.blit(carro_segundario_branco, (20, 20))  
        screen.blit(carro_segundario_azul, (100, 20)) 
    try: 
        plano_de_fundo = pygame.image.load('imagens/estrada.jpg')
        plano_de_fundo = pygame.transform.scale(plano_de_fundo, (800, 800))
    except pygame.error:
        print("Erro ao carregar a imagem de fundo.")
        return

    try:
        carro_jogador = pygame.image.load('imagens/carro_principal_verde.png').convert_alpha()
    except pygame.error:
        print("Erro ao carregar a imagem do carro.")
        return
    
    carros_segundarios_pbaixo = [carro_segundario_branco,carro_segundario_azul]

    largura_do_carro = 200
    altura_do_carro = 200
    carro_jogador = pygame.transform.scale(carro_jogador, (200, 200))

    x, y = 370, 600
    velocidade = 5
    carros_inimigos = []
    tempo_ultimo_carro = pygame.time.get_ticks()
    pontuacao = 0
    rodando = True

    while rodando:
        teclas = pygame.key.get_pressed()
        if teclas[pygame.K_LEFT] and x > 0:
            x -= velocidade
            print("X")
        if teclas[pygame.K_RIGHT] and x < 600:
            x += velocidade
            print("Y")
        
        screen.fill((0 ,0, 0))
        screen.blit(plano_de_fundo, (0, 0))
        screen.blit(carro_jogador, (x, y))
        for img in carros_segundarios_pbaixo:
            screen.blit(img, (20,20))

        
        if pygame.time.get_ticks() - tempo_ultimo_carro > 2000:
            carro_x = random.randint(0, screen.get_width() - largura_do_carro)
            carro_rect = pygame.Rect(carro_x, -altura_do_carro, largura_do_carro, altura_do_carro)
            carros_inimigos.append(carro_rect)
            tempo_ultimo_carro = pygame.time.get_ticks()

        #for carro in carros_inimigos['Imagens/carro_segundario_azul_pbaixo.png']:
        for carro in carros_inimigos:
            carro.y += velocidade / 2
            if carro.y > screen.get_height():
                carros_inimigos.remove(carro)
                pontuacao += 1
            elif carro.colliderect(pygame.Rect(x, y, 200, 200)):
                print("Colisão! Você perdeu!")
                rodando = False
        pygame.display.flip()
        clock.tick(60)

tela_inicial()
