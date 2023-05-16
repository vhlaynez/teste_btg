# Requisitos:

## Modelar os contratos (jogada1, jogada2) -> vencedor/empate
	
  ### Método HTTP, códigos de sucesso/erro, rotas

- POST /play
- body:
```json
{
  jogador1: "papel",
  jogador2: "tesoura"
}
```

- Response
- status 200 OK
```json
{
	winner: "jogador2"
}
```
### Empate
`status 204 No Content`

### Request com valores inválidos
`status 400 bad request, mensagem "parâmetro inválido"`

### Erro no servidor
`status 500, mensagem "Ocorreu um erro, por favor tente novamente mais tarde"`

## Pensar em uma modelagem clean arch
Framework(rails/sinatra)

Interface Adapter(play/valid_params?)

Aplication Logic Service(match)

## Algoritmo
```ruby
def play
	render json: { error: 'parâmetro inválido' }, status: unless valid_params?(params)
	
	render json: { winner: match(params)}, status: :ok unless match(params).nil?
	render :no_content
rescue StandardError 
  render json: { error: 'Ocorreu um erro, por favor tente novamente mais tarde' }, status: :internal_server_error
end

def valid_params?(params)
	return false if params[:jogador1].nil? || params[:jogador2].nil?
	return false unless params[:jogador1].includes?['papel','pedra','tesoura']
	return false unless params[:jogador2].includes?['papel','pedra','tesoura']
end

enum { pedra: 0, papel: 1, tesoura: 2 }
# Pelo que entendi do enunciado, o body deveria ser feito utilizando strings, por isso adicionei o enum nessa camada.
# Tambem seria possível já passar int no body da requisição.
def match(params)
	return if params[:jogador1] == params[:jogador2]
	
	if ( (params[:jogador1] - params[:jogador2] == -2) || (params[:jogador1] - params[:jogador2] == 1))
		return 'jogador1'
	end
	
	return 'jogador2'
end
```
