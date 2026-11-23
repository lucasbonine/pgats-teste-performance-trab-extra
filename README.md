# Teste de performance da API de Pets

Projeto de testes no JMeter para os endpoints `POST /pets` e `GET /pets`. Os dois modelos de carga fazem login em `POST /login` e usam o token recebido nas requisições seguintes.

## Requisitos

- Apache JMeter 5.6.3
- Java 17
- API de Pets rodando em `http://localhost:3000`

## Como executar

Na pasta da API, instale as dependências e inicie o servidor:

```bash
npm install
npm start
```

Com a API em execução, abra o arquivo `testeDePerformanceApiPets.jmx` no JMeter e clique em **Start**. Os resultados aparecem no **Summary Report**.

Também é possível executar pelo terminal:

```bash
jmeter -n -t testeDePerformanceApiPets.jmx -l resultados.jtl
```

## Configuração dos testes

| Modelo | Configuração |
| --- | --- |
| Fechado | 20 usuários, entrada gradual em 10 segundos e duração de 40 segundos. Cada usuário faz login uma vez e repete o cadastro e a consulta, com pausa de 800 ms antes de cada requisição. |
| Aberto | 4 novas jornadas por segundo durante 30 segundos. Cada jornada faz login, cadastra um pet e consulta a lista. Há uma espera inicial de 50 segundos na agenda e 8 segundos ao final para concluir as requisições pendentes. |

O grupo fechado roda primeiro porque está acima do aberto na árvore e a opção **Run Thread Groups consecutively** está marcada no Test Plan. Com essa configuração, o teste completo leva cerca de 88 segundos.

O login usa a conta de exemplo `tiago`, com senha `123456`. Os pets recebem nomes com UUID para evitar cadastros duplicados. As asserções verificam os retornos 200 no login e na consulta, e 201 no cadastro.

## Observações

- Reinicie a API antes de cada execução se quiser começar com os dados iniciais. Os cadastros ficam na memória e a lista cresce durante o teste.
- O grupo fechado pode terminar entre o cadastro e a consulta, então as quantidades finais dessas requisições podem ser diferentes.
- Para medir a carga, deixe o **View Results Tree** desativado.
- Se mudar a duração do fechado, revise também a espera inicial da agenda do aberto.
