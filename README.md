#include <stdio.h>
#include <string.h>
#include <stdlib.h> // Para system("cls") ou system("clear") para usabilidade
 
// --- Struct ---
/**
* @brief Struct que armazena as informações no inventário
* * Campos:
* - nome: Nome do item (máx. 29 caracteres).
* - tipo: Categoria do item (ex: arma, munição, cura).
* - quantidade: Número de unidades deste item.
*/
typedef struct {
    char nome[30];
    char tipo[20];
    int quantidade;
} Item;
 
// ---  Variáveis Globais (Vetor de Structs e Contador) ---
#define CAPACIDADE_MAXIMA 10
Item mochila[CAPACIDADE_MAXIMA]; //  structs vetor de  sequencial list de inventário
int totalItens = 0;              // rastreio de número real de itens na mochila
 
// --- Protótipos das Funções ---
void listarItens();
void inserirItem();
void removerItem();
void buscarItem();
void exibirMenu();
 
// ---  Implementação das Funções ---
 
/**
* @brief Lista os itens registrados na mochila now
* * Se mochila  vazia exibe mensagem
* ou se não percorre vetor 'mochila' ao 'totalItens' para amostra de informação 
* de cada item.
*/
void listarItens() {
    printf("\n### ITENS ATUAIS NA MOCHILA (%d/%d) ###\n", totalItens, CAPACIDADE_MAXIMA);
    
    if (totalItens == 0) {
        printf("> A mochila está vazia. Colete alguns itens!\n");
        return;
    }
 
    // laço 'for'  percorre vetor structs
    for (int i = 0; i < totalItens; i++) {
        printf("-------------------------------------------\n");
        printf("Índice: %d\n", i + 1);
        printf("Nome: %s\n", mochila[i].nome);
        printf("Tipo: %s\n", mochila[i].tipo);
        printf("Quantidade: %d\n", mochila[i].quantidade);
    }
    printf("-------------------------------------------\n");
}
 
/**
* @brief jogador pode gravar new item na mochila
* * checa capacidade atingida. 
* se não atingir capacidade pede nome, tipo, quantidade e armazena final do vetor
*/
void inserirItem() {
    //  check  capacidade
    if (totalItens >= CAPACIDADE_MAXIMA) {
        printf("\n❌ FALHA: Mochila cheia! Remova algum item antes de adicionar.\n");
        return;
    }
 
    printf("\n### ➕ CADASTRAR NOVO ITEM ###\n");
    Item novoItem;
 
    // ler nome (fgets para nomes com espaço)
    printf("Nome do Item (máx. 29 caracteres): ");
    // Limpa o buffer de entrada com caracteres pendentes
    int c;
    while ((c = getchar()) != '\n' && c != EOF);
    fgets(novoItem.nome, sizeof(novoItem.nome), stdin);
    // se tiver quebra linha gerada no fgets remove
    if (novoItem.nome[strlen(novoItem.nome) - 1] == '\n') {
        novoItem.nome[strlen(novoItem.nome) - 1] = '\0';
    }
 
    // ler tipo
    printf("Tipo do Item (ex: arma, munição, cura): ");
    scanf("%s", novoItem.tipo);
    
    // ler qnt (usa scanf para valores inteiros)
    printf("Quantidade: ");
    if (scanf("%d", &novoItem.quantidade) != 1) {
        printf("❌ ERRO: Entrada inválida para quantidade. Cancelando cadastro.\n");
        // Limpa buffer se  erro ao ler
        while ((c = getchar()) != '\n' && c != EOF);
        return;
    }
    
    // vetor
    mochila[totalItens] = novoItem; //  struct final do vetor
    totalItens++;                   // conta itens
    
    printf("\n✅ Item '%s' cadastrado com sucesso! Tipo: %s, Quantidade: %d.\n",
           novoItem.nome, novoItem.tipo, novoItem.quantidade);
 
    listarItens(); // Requisito: Listar itens após cada operação
}
 
/**
* @brief sequencial search item to name 
* * @param nomeBusca O nome do item a ser procurado.
* @return int O índice do item encontrado, ou -1 se não for encontrado.
*/
int buscarIndiceItem(const char *nomeBusca) {
    // laço 'for' percorre o vetor e busca sequencial
    for (int i = 0; i < totalItens; i++) {
        //  'strcmp'  comparação strings (nomes)
        //  case-sensitive search Para ser case-insensitive
        if (strcmp(mochila[i].nome, nomeBusca) == 0) {
            return i; // traz índice de item localizado
        }
    }
    return -1; // Retorna -1 se não encontrado
}
 
/**
* @brief busca item na mochila por nome e mostra informações
* * Aciona função 'buscarIndiceItem' mostra informações do item se localizado
*/
void buscarItem() {
    if (totalItens == 0) {
        printf("\n⚠️ ATENÇÃO: A mochila está vazia. Não há itens para buscar.\n");
        return;
    }
 
    char nomeBusca[30];
    printf("\n### 🔎 BUSCAR ITEM PELO NOME ###\n");
    printf("Digite o nome exato do item para buscar: ");
    
    // Limpa buffer entrada
    int c;
    while ((c = getchar()) != '\n' && c != EOF);
    fgets(nomeBusca, sizeof(nomeBusca), stdin);
    if (nomeBusca[strlen(nomeBusca) - 1] == '\n') {
        nomeBusca[strlen(nomeBusca) - 1] = '\0';
    }
 
    int indice = buscarIndiceItem(nomeBusca); //  busca sequencial, chama função
 
    if (indice != -1) {
        // Localizado item
        printf("\n✅ ITEM ENCONTRADO:\n");
        printf("-------------------------------------------\n");
        printf("Índice na Mochila: %d\n", indice + 1);
        printf("Nome: %s\n", mochila[indice].nome);
        printf("Tipo: %s\n", mochila[indice].tipo);
        printf("Quantidade: %d\n", mochila[indice].quantidade);
        printf("-------------------------------------------\n");
    } else {
        // Não localizado
        printf("\n❌ ITEM NÃO ENCONTRADO: O item '%s' não está na mochila.\n", nomeBusca);
    }
}
 
 
/**
* @brief O jogador pode excluir um item da mochila ao informando o nome
* * Ao encontrar item o mesmo é excluído movendo os demais
* uma posição para cobrir o item excluído
*/
void removerItem() {
    if (totalItens == 0) {
        printf("\n⚠️ ATENÇÃO: A mochila está vazia. Não há itens para remover.\n");
        return;
    }
 
    char nomeRemover[30];
    printf("\n### 🗑️ REMOVER ITEM POR NOME ###\n");
    printf("Digite o nome exato do item que deseja remover: ");
    
    // buffer limpeza de entrada
    int c;
    while ((c = getchar()) != '\n' && c != EOF);
    fgets(nomeRemover, sizeof(nomeRemover), stdin);
    if (nomeRemover[strlen(nomeRemover) - 1] == '\n') {
        nomeRemover[strlen(nomeRemover) - 1] = '\0';
    }
 
    int indice = buscarIndiceItem(nomeRemover); // Localiza item índice
 
    if (indice != -1) {
        // Item localizado, realiza a exclusão (desloca sequencia)
        
        // laço 'for' deslocar os elementos
        for (int i = indice; i < totalItens - 1; i++) {
            // Move item de 'i+1' para a posição 'i',
            // subscreve item a excluir
            mochila[i] = mochila[i + 1];
        }
 
        totalItens--; // decrementa contado de item
 
        printf("\n✅ Item '%s' removido com sucesso da mochila!\n", nomeRemover);
        listarItens(); // Lista itens para cada operação
    } else {
        // Não localizado
        printf("\n❌ ERRO NA REMOÇÃO : O item '%s' não foi localizado.\n", nomeRemover);
    }
}
 
/**
* @brief Mostra menu geral de opções
*/
void exibirMenu() {
    printf("\n============================================\n");
    printf("   SISTEMA DE INVENTÁRIO (MOCHILA) 🎒\n");
    printf("============================================\n");
    printf("1. Cadastrar Novo Item\n");
    printf("2. Remover Item pelo Nome\n");
    printf("3. Listar Todos os Itens\n");
    printf("4. Buscar Item pelo Nome\n");
    printf("0. Sair do Jogo\n");
    printf("--------------------------------------------\n");
    printf("Escolha uma opção: ");
}
 
// --- Função (main) ---
/**
* @brief Ponto de entrada do programa
* *laço principal  opções e gestao de fluxo de execução,
* chamando as funções conforme seleção de jogador
*/
int main() {
    int opcao;
 
    // ex itens iniciais pré-cadastrados (opcional, para testar)
    /*
    strcpy(mochila[0].nome, "Pistola");
    strcpy(mochila[0].tipo, "arma");
    mochila[0].quantidade = 1;
 
    strcpy(mochila[1].nome, "Munição Leve");
    strcpy(mochila[1].tipo, "municao");
    mochila[1].quantidade = 60;
    
    strcpy(mochila[2].nome, "Kit Médico");
    strcpy(mochila[2].tipo, "cura");
    mochila[2].quantidade = 2;
    
    totalItens = 3;
    */
 
    do {
        exibirMenu();
        // ler opção (usa scanf para valores inteiros)
        if (scanf("%d", &opcao) != 1) {
            printf("\n❌ ENTRADA INVÁLIDA. Por favor, digite um número de 0 a 4.\n");
            // Limpa buffer entrada em caso de erro
            int c;
            while ((c = getchar()) != '\n' && c != EOF);
            opcao = -1; // aciona loop a continuar
            continue;
        }
 
        // Limpa a interface
        // system("cls"); // Windows
        // system("clear"); // Linux/macOS
 
        switch (opcao) {
            case 1:
                inserirItem();
                break;
            case 2:
                removerItem();
                break;
            case 3:
                listarItens();
                break;
            case 4:
                buscarItem();
                break;
            case 0:
                printf("\n👋 Saindo do sistema de inventário. Boa sorte no jogo!\n");
                break;
            default:
                printf("\n⚠️ OPÇÃO INVÁLIDA. Por favor, escolha um número entre 0 e 4.\n");
        }
        
        // pausa  jogador  ler
        if (opcao != 0) {
            printf("\nPressione ENTER para seguir...\n");
            int c;
            while ((c = getchar()) != '\n' && c != EOF); // Limpa buffer
            getchar(); // Espera por ENTER
        }
 
    } while (opcao != 0);
 
    return 0;
}
