# ADR 002 — Escolha do banco de dados

## Status

Aceito

## Contexto

O sistema precisa armazenar dados de alunos, livros, empréstimos, devoluções e multas.

## Decisão

Será utilizado PostgreSQL como banco de dados.

## Justificativa

PostgreSQL é um banco de dados relacional gratuito, confiável e apropriado para dados que possuem relacionamento, como aluno, livro e empréstimo.

## Consequências

- O projeto deverá configurar uma conexão com PostgreSQL.
- O Docker Compose deverá possuir um serviço para o banco de dados.
- As variáveis de conexão devem ficar em arquivo de ambiente, como `.env`.
