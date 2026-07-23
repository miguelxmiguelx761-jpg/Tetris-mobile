# Tetris-mobile
Esse tetris foi feito para celular em computador para você acessar esse script você tem que instalar o python
estar aqui o script python 
👇👇

# TRABALHO EM EQUIPE: TETERES DE RESPEITO COM NOVAS CORES!
import os
import random

LARGURA = 10
ALTURA = 15
pontuacao = 0

CORES = ["🟧", "🟦", "🟥"]

tabuleiro_fixo = []
for l in range(ALTURA):
    nova_linha = []
    for c in range(LARGURA):
        nova_linha.append("⬜")
    tabuleiro_fixo.append(nova_linha)

def mostrar_jogo(pos_linha, pos_coluna, formato_peca, cor_peca):
    print("\n" * 25)
    print("=== TETERES DE RESPEITO ===")
    print(f" PONTOS: {pontuacao} ⭐")
    print(" CONTROLES: [a] Esq | [d] Dir | [s] Baixo | [w] GIRA 🔄")
    print("==============================")

    tabuleiro_com_peca = [linha[:] for list_linha in tabuleiro_fixo for linha in [list_linha]]

    for bloco_l, bloco_c in formato_peca:
        l_real = pos_linha + bloco_l
        c_real = pos_coluna + bloco_c
        if 0 <= l_real < ALTURA and 0 <= c_real < LARGURA:
            tabuleiro_com_peca[l_real][c_real] = cor_peca

    for linha in tabuleiro_com_peca:
        print("".join(linha))
    print("==============================")

def checar_colisao(pos_linha, pos_coluna, formato_peca):
    for bloco_l, bloco_c in formato_peca:
        l_futura = pos_linha + bloco_l
        c_futura = pos_coluna + bloco_c
        if l_futura >= ALTURA or c_futura < 0 or c_futura >= LARGURA:
            return True
        if tabuleiro_fixo[l_futura][c_futura] in CORES:
            return True
    return False

# FUNÇÃO DA MÁGICA: Gira os blocos usando matemática de matrizes!
def girar_formato(formato):
    # (l, c) virado vira (-c, l)
    novo_formato = [(-c, l) for l, c in formato]
    return novo_formato

# --- LOOP INFINITO DO JOGO ---
while True:
    peca_linha = 0
    peca_coluna = 4

    formatos_possiveis = [
        [(0, 0)],                        # 1. Quadradinho
        [(0, 0), (0, 1)],                 # 2. Seta lado
        [(0, 0), (1, 0)],                 # 3. Seta baixo
        [(0, 0), (0, 1), (1, 0)],         # 4. Sua Peça 1
        [(0, 0), (1, 0), (1, 1)]          # 5. Sua Peça 2
    ]

    formato_atual = random.choice(formatos_possiveis)
    cor_atual = random.choice(CORES)  # Sorteia se vai ser Laranja, Azul ou Vermelho!

    # Loop da peça caindo
    while True:
        mostrar_jogo(peca_linha, peca_coluna, formato_atual, cor_atual)

        if checar_colisao(peca_linha + 1, peca_coluna, formato_atual):
            break

        comando = input("Ação (a/d/s/w):").lower()

        if comando == "a":
            if not checar_colisao(peca_linha, peca_coluna - 1, formato_atual):
                peca_coluna -= 1
        elif comando == "d":
            # Teste de proteção para os lados antes de mover
            if not checar_colisao(peca_linha, peca_coluna + 1, formato_atual):
                peca_coluna += 1
        elif comando == "s":
            if not checar_colisao(peca_linha + 1, peca_coluna, formato_atual):
                peca_linha += 1
            else:
                break
        elif comando == "w":
            # Tenta girar a peça temporariamente para testar se ela bate na parede
            formato_girado = girar_formato(formato_atual)
            if not checar_colisao(peca_linha, peca_coluna, formato_girado):
                formato_atual = formato_girado # Só aceita o giro se não colidir!

    for bloco_l, bloco_c in formato_atual:
        l_fixa = peca_linha + bloco_l
        c_fixa = peca_coluna + bloco_c
        if 0 <= l_fixa < ALTURA and 0 <= c_fixa < LARGURA:
            tabuleiro_fixo[l_fixa][c_fixa] = cor_atual

    # --- DESTRUIR LINHA CHEIA ---
    linhas_completas = 0
    for l in range(ALTURA):
        if "⬜" not in tabuleiro_fixo[l]:
            tabuleiro_fixo.pop(l)
            tabuleiro_fixo.insert(0, ["⬜"] * LARGURA)
            linhas_completas += 1

    if linhas_completas > 0:
        pontuacao += linhas_completas * 100

    if tabuleiro_fixo[0][4] in CORES:
        print("FIM DE JOGO! Reiniciando...")
        tabuleiro_fixo = [["⬜"]*LARGURA for _ in range(ALTURA)]
        pontuacao = 0
        
