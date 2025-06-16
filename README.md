#include <stdio.h>

#define TAM 10
#define TAM_HABILIDADE 5
#define NAVIO 3

// Função para inicializar o tabuleiro com água
void inicializarTabuleiro(int tabuleiro[TAM][TAM]) {
    for (int i = 0; i < TAM; i++)
        for (int j = 0; j < TAM; j++)
            tabuleiro[i][j] = 0;
}

// Posiciona navios fixos (como no nível anterior)
void posicionarNavios(int tabuleiro[TAM][TAM]) {
    // Navio horizontal
    for (int i = 0; i < NAVIO; i++) tabuleiro[0][2 + i] = 3;

    // Navio vertical
    for (int i = 0; i < NAVIO; i++) tabuleiro[4 + i][6] = 3;

    // Diagonal principal
    for (int i = 0; i < NAVIO; i++) tabuleiro[6 + i][i] = 3;

    // Diagonal inversa
    for (int i = 0; i < NAVIO; i++) tabuleiro[i][6 - i] = 3;
}

// Cria matriz cone (formato de triângulo com vértice no topo)
void criarMatrizCone(int cone[TAM_HABILIDADE][TAM_HABILIDADE]) {
    for (int i = 0; i < TAM_HABILIDADE; i++) {
        for (int j = 0; j < TAM_HABILIDADE; j++) {
            if (j >= (TAM_HABILIDADE - 1) / 2 - i && j <= (TAM_HABILIDADE - 1) / 2 + i)
                cone[i][j] = 1;
            else
                cone[i][j] = 0;
        }
    }
}

// Cria matriz cruz
void criarMatrizCruz(int cruz[TAM_HABILIDADE][TAM_HABILIDADE]) {
    for (int i = 0; i < TAM_HABILIDADE; i++) {
        for (int j = 0; j < TAM_HABILIDADE; j++) {
            if (i == (TAM_HABILIDADE - 1) / 2 || j == (TAM_HABILIDADE - 1) / 2)
                cruz[i][j] = 1;
            else
                cruz[i][j] = 0;
        }
    }
}

// Cria matriz octaedro (forma de losango)
void criarMatrizOctaedro(int oct[TAM_HABILIDADE][TAM_HABILIDADE]) {
    int centro = (TAM_HABILIDADE - 1) / 2;
    for (int i = 0; i < TAM_HABILIDADE; i++) {
        for (int j = 0; j < TAM_HABILIDADE; j++) {
            if (abs(i - centro) + abs(j - centro) <= centro)
                oct[i][j] = 1;
            else
                oct[i][j] = 0;
        }
    }
}

// Sobrepõe matriz de habilidade ao tabuleiro
void aplicarHabilidade(int tabuleiro[TAM][TAM], int habilidade[TAM_HABILIDADE][TAM_HABILIDADE], int origemLinha, int origemColuna) {
    int offset = TAM_HABILIDADE / 2;

    for (int i = 0; i < TAM_HABILIDADE; i++) {
        for (int j = 0; j < TAM_HABILIDADE; j++) {
            int linhaTab = origemLinha - offset + i;
            int colunaTab = origemColuna - offset + j;

            if (linhaTab >= 0 && linhaTab < TAM && colunaTab >= 0 && colunaTab < TAM) {
                if (habilidade[i][j] == 1 && tabuleiro[linhaTab][colunaTab] == 0) {
                    tabuleiro[linhaTab][colunaTab] = 5;
                }
            }
        }
    }
}

// Exibe o tabuleiro com símbolos personalizados
void exibirTabuleiro(int tabuleiro[TAM][TAM]) {
    printf("    ");
    for (int j = 0; j < TAM; j++) printf("%2d ", j);
    printf("\n");

    for (int i = 0; i < TAM; i++) {
        printf("%2d |", i);
        for (int j = 0; j < TAM; j++) {
            if (tabuleiro[i][j] == 0)
                printf(" ~ ");
            else if (tabuleiro[i][j] == 3)
                printf(" N ");
            else if (tabuleiro[i][j] == 5)
                printf(" * ");
        }
        printf("\n");
    }
}

int main() {
    int tabuleiro[TAM][TAM];
    int cone[TAM_HABILIDADE][TAM_HABILIDADE];
    int cruz[TAM_HABILIDADE][TAM_HABILIDADE];
    int octaedro[TAM_HABILIDADE][TAM_HABILIDADE];

    // Inicializações
    inicializarTabuleiro(tabuleiro);
    posicionarNavios(tabuleiro);
    criarMatrizCone(cone);
    criarMatrizCruz(cruz);
    criarMatrizOctaedro(octaedro);

    // Aplicar habilidades no tabuleiro com ponto de origem
    aplicarHabilidade(tabuleiro, cone, 2, 2);       // Cone em (2,2)
    aplicarHabilidade(tabuleiro, cruz, 7, 7);       // Cruz em (7,7)
    aplicarHabilidade(tabuleiro, octaedro, 5, 2);   // Octaedro em (5,2)

    // Exibir resultado final
    printf("\n=== TABULEIRO FINAL ===\n");
    exibirTabuleiro(tabuleiro);

    return 0;
}
