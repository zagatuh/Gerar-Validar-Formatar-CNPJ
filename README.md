# Gerar-Validar-Formatar-CNPJ
Programa que utiliza funções para gerar, validar e formatar um CNPJ em C

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
 
#define CNPJ_OK 0 // caso retorne esse valor o CNPJ é verdadeiro
#define DV1 -1 // caso retorne esse valor o DV1 será inválido
#define DV2 -2 // caso retorne esse valor o DV2 será inválido
 
int calcular_cnpj(char cnpj[], int total) {
 
    int i, peso, soma, dv, pos;
 
    peso = (total == 12) ? 5 : 6; // peso começa com 5 e vai até o 2, e na segunda reptição começa em 6
    soma = 0;
    for(i = 0; i < total; i++) { // repetição dos 12 digitos do CNPJ
        soma += ((cnpj[i] - 48) * peso--);  // soma ira receber o char na posição [i] convertida para um inteiro multiplicado pelo peso
        pos = (total == 12) ? 3 : 4; // na quarta repetição, antes de ir para a quinta, o peso deverá receber o valor de 9, poís é essa a sequência da multiplicação para CNPJ
    if(i == pos)
        peso = 9;
    }
    dv = 11 - (soma % 11); // DV irá receber o resto da divisão subtraindo 11
 
    if(dv == 10 || dv == 11) // caso DV seja igual à 10 ou 11 ele receberá o valolr de 0
       return 0;
 
    return dv;
}
 
void gerar_cnpj(char cnpj[]) {
 
    int i;
 
    for(i = 0; i < 14; i++) {
        cnpj[i] = (rand() % 10) + 48;   
 
    cnpj[12]  = cnpj[13] = '0';
    cnpj[12]  = calcular_cnpj(cnpj, 12) + 48;
    cnpj[13] = calcular_cnpj(cnpj, 13) + 48;
    cnpj[14] = '\0';
 
    if(i >= 8 && i <= 10)
        cnpj[i] = '0';
    if(i == 11)
        cnpj[i] = '1';
    }
}
 
void formatar_cnpj(char cnpj_origem[], char cnpj_destino[]) { // função para formatar o CNPJ com seus deviso caracteres 
   strncpy(cnpj_destino, cnpj_origem, 2); // (cnpj_destino) ira receber os dois primeiros dois caracteres do (cnpj_origem) nos indices [0] e [1]
   cnpj_destino[2] = '.'; 
   strncpy(&cnpj_destino[3], &cnpj_origem[2], 3);
   cnpj_destino[6] = '.';
   strncpy(&cnpj_destino[7], &cnpj_origem[5], 3);
   cnpj_destino[10] = '/';
   strncpy(&cnpj_destino[11], &cnpj_origem[8], 4); 
   cnpj_destino[15] = '-';
   strncpy(&cnpj_destino[16], &cnpj_origem[12], 2);
}
 
int validar_cnpj(char cnpj[]) { // função que irá validar os DVs
 
    int i, pos, dv, dv_cnpj;
 
    for(i = 1; i <= 2; i++) { // a variavel (i) irá fazer com que o for repita apenas 2 vezes, já que só temos dois verificadores
        int total = (i == 1) ? strlen(cnpj) - 2 : strlen(cnpj) - 1; //  caso (i) esteja na primeira repetição, total irá receber CNPJ - 2, se não irá receber CNPJ - 1
        dv = calcular_cnpj(cnpj, total); // usamos a função de calcular os DVs para recebermos os dois
        pos = (i == 1) ? strlen(cnpj) - 2 : strlen(cnpj) - 1; // caso seja a primeira repetição ele utilizara a posição 12 do CNPJ 
        dv_cnpj = cnpj[pos] - 48;  // os DVs irão retornar ao (dv_cnpj) seus valores
 
        if(dv != dv_cnpj) // compara os dois DVs, caso algum esteja errado ele ira retornar o errado
            if(i == 1)  
                return DV1;
            else 
                return DV2;
 
    }
    return CNPJ_OK;
}
 
int main() {
 
    char cnpj[15];
    int resultado;
 
    srand(time(NULL));  
    gerar_cnpj(cnpj);
    
    //printf("Informe seu CNPJ: ");
    //gets(cnpj);
 
    resultado = validar_cnpj(cnpj);
 
    if(resultado == DV1)
        printf("Erro no DV1\n");
    else 
        if(resultado == DV2)
            printf("Erro no DV2\n");
        else {            
            char cnpj_formatado[19] = { 0 };
            formatar_cnpj(cnpj, cnpj_formatado);
            printf("CNPJ: %s válido", cnpj_formatado);
        }
}
