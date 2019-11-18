1. **Discuta suscintamente e compare as técnicas de Materialização e Pipelining para avaliação de consulta.**
> Na **Materialização** os resultados das operações são armazenadas em disco como se fossem relações reais. Avalia um operador de cada vez, começando da folha para  a raiz (_bottom-up_). E usa os resultados intermediários materializados em relações temporários para avaliar as operações dos níveis acima. As escritas e leituras dessas relações podem ser altos.
> 
> Já na técnica de **Pipelining** os registros intermediários são encaminhados para a operação "pai" assim que gerados. Várias expressões são avaliadas simultaneamente. Tem um custo inferior mas nem sempre é aplicável (eg. hash-join, sort, etc). Pode ainda ser Orientado à Produção (push) ou Sob Demanta (pull).


2. **Enumere e descreva os estados que uma transação pode assumir. Descreva também as ocorrências que causam as transições destes estados.**
> ![estados-transação](https://user-images.githubusercontent.com/13461315/33643365-03f842c0-da15-11e7-83c2-96800ea2f6a8.PNG)


3. **Considere a consulta abaixo.... Desenhe pelo menos duas árvores de consulta que podem representar essa consulta e compare as duas no que diz respeito a sua otimização no nível da álgebra relacional.**
```sql
SELECT E.PNOME, E.UNOME, E.ENDERECO
FROM EMPREGADO E, DEPARTAMENTO D
WHERE D.DNOME='Pesquisa' AND D.NUMERO=E.DNUM
```
> Temos uma versão com a seleção no ponto mais alto possível e outra com a mesma sendo executada mais cedo. Como o operador de seleção retorna uma relação onde o número de tuplas é menor ou igual ao original, geralmente esta relação terá menos tuplas, assim, o produto cartesiando que é realizado depois terá um impacto menor no custo pois menos tuplas serão processadas.
> ![v1](https://user-images.githubusercontent.com/13461315/33644085-2eeffb5e-da19-11e7-8d94-1525f21dd45a.PNG)


4. **Com relação ao Algoritmo Merge-Join pede-se: esboço deste algoritmo em alto nível**
> Se os registros R e S estiverem fisicamente ordenados por valor dos atributos de junção A e B, respectivamente, podemos implementar a junção da maneira mais eficiente possível. Os dois arquivos são varridos simultaneamente na ordem dos atributos de junção, combinando os registro que têm os mesmos valores para A e B.
> A imagem abaixo apresenta um pseudocódigo assumindo que A e B não estão ordenados [foi retirada de ftp://vm1-dca.fee.unicamp.br/pub/docs/ricarte/apostilas/spbdaa.pdf].
![mergejoinsort](https://user-images.githubusercontent.com/13461315/33644298-4330e122-da1a-11e7-9f6b-557183ae977b.PNG)


5. **Considerando as transações abaixo, use primitivas de bloqueio compartilhado ou exclusivo e de desbloqueio para construir:**

| T1 | T2 | T3 |
|----|---|---|
| read_item(X) | read_item(Z) | read_item(Y) |
| write_item(X) | read_item(Y) | read_item(Z) |
| read_item(Y) | write_item(Y) | write_item(Y) |
| write_item(Y) | read_item(X) | write_item(Z) |
||write_item(X)||

     1. Uma transação com 2PL não conservativo e não estrito a partir de T1;
   > | T |
   > |---|
   > | lock(x) | 
   > | read_item(X) |
   > | write_item(X) |
   > | lock(y) | 
   > | unlock(x) |
   > | read_item(y) |
   > | write_item(y) | 
   > | unlock(y) |

     2. Uma transação com 2PL conservativo e não estrito a partir de T2;
   > | T |
   > |---|
   > | read_lock(z) | 
   > | write_lock(y) | 
   > | write_lock(x) | 
   > | read_item(z) |
   > | unlock(z) |
   > | read_item(y) |
   > | write_item(y) |
   > | unlock(y) | 
   > | read_item(x) |
   > | write_item(X) |
   > | unlock(x) |

     3. Uma transação com 2PL não conservativo e estrito a partir de T3;
   > | T |
   > |---|
   > | read_lock(y) |
   > | read_item(y) |
   > | read_lock(z) |
   > | read_item(z) |
   > | write_lock(y) |
   > | write_item(y) | 
   > | unlock(y) |
   > | write_lock(z) |
   > | write_item(z) |
   > | unlock(y) |



6. **Considerando as transações da questão anterior**
     1. **Apresente um escalonamento não-serial destas transações e mostre que ele é serializável usando um grafo de precedência**
        > | T1 | T2 | T3 |
        > |---|----|---|
        > | r(x) | |
        > | w(x) | |
        > | | r(z) |
        > | r(y) | |
        > | w(y) | |
        > | | r(y) |
        > | | w(y) |
        > | | | r(y)
        > | | | r(z)
        > | | r(x)  |
        > | | w(x) |
        > | | | w(y)
        > | | | w(z)

        > ![6b](https://user-images.githubusercontent.com/13461315/33647522-d9048cce-da2b-11e7-9532-35beb4729f08.png)
        > Um escalonamento serial equivalente seria: **T1 → T2  → T3**

     2. **Apresente um escalonamento não-serial destas transações e mostre que ele é não serializável (quanto ao conflito) usando um grafo de precedência**
        > | T1 | T2 | T3 |
        > |---|----|---|
        > | r(x) | |
        > | w(x) | |
        > | | r(z) |
        > | | r(y) |
        > | | | r(y)
        > | | | r(z)
        > | r(y) | |
        > | w(y) | |
        > | | w(y) |
        > | | r(x)  |
        > | | w(x) |
        > | | | w(y)
        > | | | w(z)

        >  ![6c](https://user-images.githubusercontent.com/13461315/33647496-b99ed060-da2b-11e7-913c-2258feb63bd5.png)
