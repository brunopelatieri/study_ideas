# 🤖 Sistema de Gestão de Prompts XML (Gemini + n8n + Supabase)

Este repositório documenta a padronização de Engenharia de Prompt em XML e o pipeline de automação desenvolvido para garantir a integridade e versionamento dos prompts utilizados no **DiretorIA App**.

---

## 🏛️ Estrutura de Tags XML (Padrão Gemini)

A utilização de tags XML permite que os modelos Gemini (1.5 Pro/Flash) processem instruções de forma hierárquica, separando claramente as diretrizes de sistema dos dados de entrada.

### 📋 Relação de Tags Principais

| Tag | Descrição | Importância |
| :--- | :--- | :--- |
| `<prompt>` | Tag raiz que encapsula todo o conteúdo. | Obrigatório (inclui atributo `version`). |
| `<system_instructions>` | Definição da Persona e Role do agente. | Crucial para o comportamento do modelo. |
| `<brazilian_context_rules>` | Regras de localização e regionalismos. | Garante precisão em Moeda (R$) e ABNT. |
| `<thought_process>` | Ativação de raciocínio lógico (CoT). | Melhora a performance em tarefas complexas. |
| `<output_format>` | Definição do formato de saída (JSON). | Essencial para o parsing no n8n. |
| `<few_shot_examples>` | Exemplos reais de Entrada/Saída. | Reduz drasticamente a taxa de alucinação. |
| `<error_handling>` | Instruções para cenários de falha. | Define comportamento para dados nulos ou ilegíveis. |
| `<normalization_patterns>` | Regras de limpeza de strings e dados. | Mantém a consistência dos dados salvos. |

---

## ⚙️ CI/CD de Prompts: Fluxo de Automação

Implementamos um fluxo de validação onde cada `push` no GitHub dispara um webhook para o n8n, que valida a estrutura XML antes de atualizar o banco de dados.


### 1. Script de Validação (`validator.py`)
Este script Python é executado pelo nó `Execute Command` no n8n para validar a sintaxe XML.

```python
import xml.etree.ElementTree as ET
import json
import sys

def validate_prompt(xml_string):
    """Valida se o XML está bem formado e contém as tags obrigatórias."""
    required_tags = ['system_instructions', 'output_format']
    try:
        root = ET.fromstring(xml_string)
        missing = [tag for tag in required_tags if root.find(tag) is None]
        
        if missing:
            return {"status": "error", "message": f"Tags obrigatórias ausentes: {missing}"}
        
        return {"status": "success", "message": "XML estruturado corretamente."}
    except ET.ParseError as e:
        return {"status": "error", "message": f"Erro de sintaxe XML: {str(e)}"}

if __name__ == "__main__":
    # Captura o conteúdo enviado pelo n8n via stdin
    input_data = sys.stdin.read()
    if input_data:
        result = validate_prompt(input_data)
        print(json.dumps(result))