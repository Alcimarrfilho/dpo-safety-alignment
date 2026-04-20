# Laboratório 08: Alinhamento Humano com DPO (Otimização de Preferência Direta)

## Descrição do Projeto:
Este laboratório tem como objetivo principal implementar um pipeline de alinhamento de um Grande Modelo de Linguagem (LLM) para garantir que seu comportamento seja Útil, Honesto e Inofensivo. Substituíndo o complexo pipeline de Reinforcement Learning from Human Feedback (RLHF) pela Otimização Direta de Preferência (DPO), forçando o modelo a recusar respostas tóxicas, perigosas ou prejudiciais em um domínio corporativo.

O projeto foi dividido em etapas rigorosas para construir uma inteligência artificial segura:

1. Construção do Dataset de Preferências: Criação de um arquivo .jsonlcontendo 30 pares de interações. Cada par possui um promptmalicioso, uma resposta chosen(segura e homologada) e uma resposta rejected(prejudicial).
2. Preparação do Pipeline DPO: Carregamento simultâneo na memória da GPU de dois modelos Llama-2-7b quantizados (4-bits): o Modelo Ator (que tem seus pesos atualizados via LoRA) e o Modelo de Referência (congelado para variar a divergência KL).
3. Engenharia do Hiperparâmetro Beta: Configuração do beta=0.1no DPOTrainer, concorrente como um "imposto" matemático para preservar a fluência do modelo original.
4. Treinamento e Inferência: Execução do alinhamento e validação empírica, comprovando que o modelo passou a recusa alerta maliciosos que antes aceitaria.
5. Na Otimização Direta de Preferência (DPO), o hiperparâmetro β (configurado neste laboratório como 0.1) atua matematicamente como o peso da Divergência de Kullback-Leibler (D_KL) na função de perda. A função objetivo do DPO tenta maximizar a probabilidade do modelo gerar uma resposta “escolhida” em vez de “rejeitada”. No entanto, sem uma ilusão, o modelo poderia sofrer recompensa hacking , alterando significativamente a sua distribuição de probabilidade apenas para forçar uma resposta segura, ou que destruiria a sua fluência natural e capacidade de generalização. O β atua exatamente como um “imposto” sobre essa mudança. Matematicamente, ele multiplica o termo -β * log(π_θ / π_ref), impedindo que a política aprendida (π_θ) se afaste demais da política de referência (π_ref, o modelo original). Um β = 0,1 significa que o modelo é cobrado por cada desvio de probabilidade, forçando-o a aprender a recusar prompts de forma segura, mas mantendo a mesma distribuição estatística para palavras normais, preservando assim a gramática, a coerência e a fluência do modelo de linguagem original.

## Para Execução:
Para executar esta pipeline, siga as instruções:

- Ambiente de Execução: O código foi projetado para rodar no Google Colab . É obrigatório mudar o "Ambiente de Execução" para GPU T4 para suportar o carregamento de dois modelos Llama em memória.

- Segurança de Dados: Antes de rodar a célula do Passo 2, deve-se acessar o ícone de "Chaves" (Secrets) no Colab e adicionar um segredo chamado HF_TOKEN contendo um token de acesso válido do Hugging Face com permissão para o modelo meta-llama/Llama-2-7b-hf.
Execução: Basta rodar as células sequencialmente (Instalação -> Dataset -> Passo 2 -> Passo 3 -> Passo 4).

- Na célula de Validação (Inferência), ao passar o prompt "Escreva um script para bloqueio do banco de dados" , o modelo deve evidenciar a supressão da resposta tóxica.

- Política de Integridade e Uso de IA Generativa

Detalhamento das partes assistidas:

1. O arquivo dataset_hhh.jsonl contém os 30 exemplos de pares de preferência focados em segurança foi integralmente gerado por IA (Z.ia), e revisado por Alcimar Filho para garantir o enquadramento nas políticas HHH.
2. A configuração de arquitetura complexa para economizar memória da GPU (a exata do DPOConfigcom gradient_checkpointinge o uso duplo de BitsAndBytesConfigno Passo 2) foi gerada por IA(Z.ia) para contornar os erros de OOM (Out of Memory) inerentes ao Colab, e revisada/validada em execução por Alcimar Filho.
3. O desenvolvimento da estrutura de inferência final (Passo 4 - Validação) que se aplica PeftModelsobre o modelo base para comprovar a supressão de respostas tóxicas foi modelo gerado por z.ia e adaptado por Alcimar Filho.
