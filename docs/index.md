**Project Overview**
- **Name:** API de CEP
- **Description:** Serviço simples em FastAPI que consulta informações de endereço a partir de um CEP brasileiro, utilizando o serviço externo ViaCEP.

**Quick Links**
- **Código:** [catalog-info.yaml](catalog-info.yaml)
- **OpenAPI / Swagger:** [openapi.yaml](openapi.yaml)
- **Dockerfile:** [dockerfile](dockerfile)

**Requirements**
- Python 3.11+
- See `requirements.txt` for Python dependencies (`fastapi`, `uvicorn`, `httpx`).

**Install (local)**
1. Create a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate
```
2. Install dependencies:

```bash
pip install -r requirements.txt
```

**Run (local)**
- Development (auto-reload):

```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

- The API will be available at `http://localhost:8000` and the automatic docs at `http://localhost:8000/docs`.

**Run with Docker**
1. Build image:

```bash
docker build -t api-cep:latest .
```
2. Run container:

```bash
docker run -p 8000:8000 api-cep:latest
```

**API Endpoint**
- GET `/cep/{cep}` — Consulta CEP via ViaCEP

Parameters:
- `cep` (path, string): CEP brasileiro com ou sem hífen. Example: `01310-100` or `01310100`.

Responses:
- `200 OK` — JSON with address fields (see `openapi.yaml` for schema). Example fields: `cep`, `logradouro`, `bairro`, `localidade`, `uf`, `ibge`, `ddd`.
- `400 Bad Request` — CEP inválido (wrong length or non-digit characters).
- `404 Not Found` — CEP não encontrado (ViaCEP returns erro).
- `502 Bad Gateway` — Falha ao consultar o serviço ViaCEP (upstream error).

Example request:

```bash
curl -sS http://localhost:8000/cep/01310-100
```

Example successful response (abridged):

```json
{
	"cep": "01310-100",
	"logradouro": "Avenida Paulista",
	"bairro": "Bela Vista",
	"localidade": "São Paulo",
	"uf": "SP"
}
```

**Implementation notes**
- The app is implemented in `main.py` using FastAPI and `httpx.AsyncClient` to call https://viacep.com.br/ws/{cep}/json/.
- Input validation: strips hyphen, checks for 8 digits; returns `400` for invalid CEPs.
- Upstream errors and missing CEPs are converted to `502` and `404`, respectively.

**OpenAPI / Docs**
- The OpenAPI specification is provided in [openapi.yaml](openapi.yaml) and the FastAPI-generated Swagger UI is available at `/docs` when running the app.

**Backstage / Catalog**
- Component metadata is in [catalog-info.yaml](catalog-info.yaml) for Backstage integration.

**Next steps & suggestions**
- Add tests for input validation and integration (mock ViaCEP).
- Add CI pipeline to run linting and tests and to build the Docker image.

If you want, I can add a small README, tests, or CI workflow next.
