# Farienergy / Aplicacion — Plantilla de Proyecto

Plantilla base con arquitectura Integra Evolucionada, scripts de bootstrap, asistentes locales para Continue y checkpoints de trazabilidad.

## Requisitos
- Git 2.30+
- Uno de:
  - PowerShell 5.1+ o PowerShell 7+
  - Bash (macOS/Linux/WSL/Git Bash)
- Acceso a un remoto (GitHub/GitLab/Bitbucket) si harás push
- En Continue Hub: secreto OPENAI_API_KEY configurado

## Opciones de uso

### Opción A: Usar como Template (GitHub recomendado)
1. Publica este repo en GitHub y márcalo como Template (Settings > General > Template repository)
2. Crea un nuevo repo desde “Use this template”
3. Clona el repo nuevo y ejecuta el bootstrap para parametrizar Cliente/Proyecto y hacer push inicial

### Opción B: Clonar y reconfigurar remoto
1. Clona este boilerplate
2. Ejecuta el bootstrap para parametrizar y cambiar el remoto

## Bootstrap (genera estructura, commit inicial, remoto y push)

- PowerShell (Windows):

```powershell
# Ejecutar con permisos por proceso (sin cambiar la política global)
powershell -ExecutionPolicy Bypass -File ./scripts/bootstrap.ps1 -Client "Farienergy" -Project "ProyectoX" -Origin "https://github.com/ORG/ProyectoX.git" -Push
```

- Bash (macOS/Linux/WSL/Git Bash):

```bash
./scripts/bootstrap.sh Farienergy ProyectoX --origin https://github.com/ORG/ProyectoX.git --push
```

Qué hacen los scripts:
- Regeneran/aseguran la estructura base y archivos mínimos
- Inicializan git (si falta), aseguran rama main, hacen commit inicial
- Configuran/actualizan el remoto origin (si se pasa) y hacen push (si se solicita)
- Crean un checkpoint con fecha UTC en Checkpoints/

## Uso detallado de los scripts

- PowerShell:
  - Parámetros:
    - -Client "Farienergy" (por defecto)
    - -Project "Aplicacion" (por defecto)
    - -Origin "https://github.com/ORG/REPO.git" (opcional)
    - -Push (opcional)
  - Ejemplos:
    - Solo local: `./scripts/bootstrap.ps1 -Client "Farienergy" -Project "Aplicacion"`
    - Con remoto y push: `./scripts/bootstrap.ps1 -Client "Farienergy" -Project "Aplicacion" -Origin "https://github.com/ORG/REPO.git" -Push`

- Bash:
  - Argumentos posicionales: CLIENT PROJECT
  - Flags: `--origin <URL>` | `--origin=<URL>`, `--push`
  - Ejemplos:
    - Solo local: `./scripts/bootstrap.sh Farienergy Aplicacion`
    - Con remoto y push: `./scripts/bootstrap.sh Farienergy Aplicacion --origin https://github.com/ORG/REPO.git --push`

## Estructura
- context/ — arquitectura y decisiones
- context/varios/ — notas varias
- propuestas/ — alternativas y POCs
- Checkpoints/ — hitos y estados del proyecto
- meta/ — plantillas de control
- scripts/ — automatizaciones (bootstrap, ingest-docs)
- api/ — endpoints/contratos (placeholder)
- .continue/assistants/ — asistentes locales de Continue (Aria e Inés)
- Archivos clave: `.gitignore`, `.env.example`, `PROYECTO.md`, `bootstrap.md`

## Asistentes de Continue (locales)
- Ubicación: `.continue/assistants`
- Secreto necesario: `OPENAI_API_KEY` en Continue Hub (Hub > Secrets)
- Modelos (predeterminados y respaldo):
  - Aria (Arquitecta): default `gpt-5`, respaldo `gpt-4o`
  - Inés (Ejecutora): default `gpt-4o`, respaldo `gpt-5`
- Si tu API no soporta `gpt-5`:
  - Aria caerá a `gpt-4o` como respaldo
  - Puedes retirar `gpt-5` del arreglo `models` en los JSON si deseas forzar su ausencia

## Flujo de trabajo recomendado
- PROYECTO.md como fuente de verdad
- Commits pequeños y checkpoints en cada bootstrap/entrega
- Documentar decisiones en `context/` y riesgos/supuestos en `context/dossier_tecnico.md`

## Troubleshooting
- No hace push:
  - Verifica URL y permisos del remoto (`git remote -v`)
  - Autenticación HTTPS/SSH correcta
- PowerShell bloquea scripts:
  - Usa ejecución por proceso: `powershell -ExecutionPolicy Bypass -File ./scripts/bootstrap.ps1 ...`
- Modelos no disponibles (p. ej. `gpt-5`):
  - La Arquitecta usará `gpt-4o` como respaldo automáticamente
  - Ajusta los JSON en `.continue/assistants/` si quieres forzar modelos específicos

## Licencia
- MIT (ver LICENSE)

## Estado
- Ver `PROYECTO.md` (Flujo de estados y Backlog)

## Ingesta de documentos (PDF, DOCX, XLSX)
- Conversión a formatos de texto para que Aria (Arquitecta) pueda contextualizarse:
  - Bash: `./scripts/ingest-docs.sh docs_ingested assets/spec.pdf assets/requisitos.docx data/tablas.xlsx`
  - PowerShell: `./scripts/ingest-docs.ps1 -OutputDir docs_ingested assets/spec.pdf assets/requisitos.docx data/tablas.xlsx`
- Requisitos:
  - PDF: pdftotext (poppler-utils) o pandoc
  - DOCX: pandoc
  - XLSX: xlsx2csv (pip) o LibreOffice/soffice
- Salida:
  - Archivos .txt, .md o .csv en la carpeta elegida (por defecto docs_ingested)
- Carga al asistente:
  - Copia/pega o arrastra los archivos convertidos en Continue para que Aria los use como contexto
