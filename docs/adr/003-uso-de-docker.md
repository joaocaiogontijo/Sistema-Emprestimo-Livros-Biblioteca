# ADR 003 — Uso de Docker

## Status

Aceito

## Contexto

O projeto precisa ser simples de executar em diferentes computadores e ambientes.

## Decisão

O projeto utilizará Docker e Docker Compose.

## Justificativa

Docker permite que todos os integrantes executem o projeto com as mesmas dependências, versões e configurações.

## Consequências

- Será criado um Dockerfile.
- Será criado um arquivo docker-compose.yml.
- As instruções de execução por Docker deverão estar no README.
