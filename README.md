# CARNAVAL-2026

<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        #filtro-carnaval { font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; max-width: 900px; margin: 20px auto; padding: 20px; border: 1px solid #eee; border-radius: 12px; background: #fff; color: #333; }
        #filtro-carnaval h2 { color: #d32f2f; text-align: center; margin-bottom: 25px; text-transform: uppercase; letter-spacing: 1px; }
        
        /* Controles em Grid para acomodar o novo filtro de nome */
        .controles { display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: 15px; margin-bottom: 25px; background: #fdf2f2; padding: 20px; border-radius: 10px; border: 1px solid #ffcdd2; }
        .controles div.busca-nome { grid-column: 1 / -1; } /* Busca por nome ocupa a linha toda */
        
        .controles label { display: block; font-size: 0.75rem; font-weight: bold; margin-bottom: 5px; color: #b71c1c; }
        .controles select, .controles input { padding: 12px; border-radius: 6px; border: 1px solid #e0e0e0; width: 100%; cursor: pointer; background: white; font-size: 1rem; box-sizing: border-box; }
        .controles select:focus, .controles input:focus { outline: none; border-color: #d32f2f; box-shadow: 0 0 0 2px rgba(211,47,47,0.1); }
        
        #lista-blocos { display: grid; gap: 12px; }
        .bloco-card { background: white; padding: 16px; border-radius: 8px; border-left: 6px solid #d32f2f; box-shadow: 0 2px 8px rgba(0,0,0,0.06); border-top: 1px solid #f5f5f5; border-right: 1px solid #f5f5f5; border-bottom: 1px solid #f5f5f5; transition: all 0.2s ease; }
        .bloco-card:hover { transform: translateY(-3px); box-shadow: 0 4px 12px rgba(0,0,0,0.12); }
        .bloco-nome { font-weight: 800; color: #d32f2f; font-size: 1.1rem; margin-bottom: 6px; line-height: 1.2; }
        .bloco-info { font-size: 0.9rem; color: #555; line-height: 1.6; }
        .bloco-info strong { color: #222; }
        .tag-dia { display: inline-block; background: #d32f2f; color: #fff; padding: 3px 10px; border-radius: 4px; font-size: 0.75rem; font-weight: bold; margin-bottom: 10px; }
        .vazio { text-align: center; color: #999; padding: 50px; font-size: 1.1rem; }
        .contador { text-align: right; font-size: 0.8rem; color: #777; margin-bottom: 10px; }
    </style>
</head>
<body>

<div id="filtro-carnaval">
    <h2>Programação Carnaval BH 2026</h2>
    
    <div class="controles">
        <div class="busca-nome">
            <label>NOME DO BLOCO</label>
            <input type="text" id="filtro-nome" placeholder="Digite o nome do bloco...">
        </div>

        <div>
            <label>DIA</label>
            <select id="filtro-dia">
                <option value="">Todos os dias</option>
                <option value="Sexta-feira, 13 de fevereiro">Sexta, 13/02</option>
                <option value="Sábado, 14 de fevereiro">Sábado, 14/02</option>
                <option value="Domingo, 15 de fevereiro">Domingo, 15/02</option>
                <option value="Segunda-feira, 16 de fevereiro">Segunda, 16/02</option>
                <option value="Terça-feira, 17 de fevereiro">Terça, 17/02</option>
                <option value="Quarta-feira, 18 de fevereiro">Quarta, 18/02</option>
            </select>
        </div>

        <div>
            <label>BAIRRO</label>
            <select id="filtro-bairro">
                <option value="">Todos os bairros</option>
            </select>
        </div>

        <div>
            <label>TURNO</label>
            <select id="filtro-horario">
                <option value="">Qualquer horário</option>
                <option value="manha">Manhã (até 11:59)</option>
                <option value="tarde">Tarde (12:00 às 17:59)</option>
                <option value="noite">Noite (após 18:00)</option>
            </select>
        </div>
    </div>

    <div class="contador" id="contador"></div>
    <div id="lista-blocos"></div>
</div>

<script>
const blocos = [
    // SEXTA 13/02
    { n: "Margarida Perfumada MP", d: "Sexta-feira, 13 de fevereiro", h: "15:30", l: "Rua Dez de Novembro, 176", b: "São Tomáz" },
    { n: "Bloco Timbaleiros do Ghetto", d: "Sexta-feira, 13 de fevereiro", h: "17:00", l: "Rua Aarão Reis, 585", b: "Centro" },
    { n: "Sexta, Ninguém Sabe?", d: "Sexta-feira, 13 de fevereiro", h: "17:00", l: "Rua Alvarenga Peixoto, 991", b: "Santo Agostinho" },
    { n: "WS Elétrico", d: "Sexta-feira, 13 de fevereiro", h: "17:00", l: "Rua Sergipe, 841", b: "Savassi" },
    { n: "Bloco da Sol", d: "Sexta-feira, 13 de fevereiro", h: "17:00", l: "Rua Vinte e Cinco de Agosto, 444", b: "Aparecida" },
    { n: "Bloco Diretucada", d: "Sexta-feira, 13 de fevereiro", h: "17:00", l: "Avenida Álvares Cabral, 366", b: "Lourdes" },
    { n: "Passa Lá no RH e Me Chama Que Eu Vou", d: "Sexta-feira, 13 de fevereiro", h: "17:00", l: "Rua Tomé de Souza, 840", b: "Savassi" },
    { n: "Ô Doral, Pega no Meu Braço!", d: "Sexta-feira, 13 de fevereiro", h: "17:00", l: "Rua Leonil Prata, 535", b: "Alípio de Melo" },
    { n: "Prove Primeiro", d: "Sexta-feira, 13 de fevereiro", h: "17:00", l: "Rua Jacana, 26", b: "Providência" },
    { n: "Bloco do Grifo", d: "Sexta-feira, 13 de fevereiro", h: "17:30", l: "Avenida Coronel Oscar Paschoal, 155", b: "São Luiz" },
    { n: "Os Meninos da Meia-Noite", d: "Sexta-feira, 13 de fevereiro", h: "17:30", l: "Rua Sete de Abril, 485", b: "Esplanada" },
    { n: "Pagode do Paulinho", d: "Sexta-feira, 13 de fevereiro", h: "18:00", l: "Rua Alberto Cintra, 365", b: "União" },
    { n: "Bloco Afro-Periférico Orisamba", d: "Sexta-feira, 13 de fevereiro", h: "18:00", l: "Rua Fagundes Varela, 95", b: "Senhor dos Passos" },
    { n: "Bloco Intervalo", d: "Sexta-feira, 13 de fevereiro", h: "18:00", l: "Rua David Campista, 42", b: "Floresta" },
    { n: "Bloco no Caminho", d: "Sexta-feira, 13 de fevereiro", h: "18:30", l: "Rua Doutor Mario Magalhães, 506", b: "Itapoã" },
    { n: "Roda de Timbau", d: "Sexta-feira, 13 de fevereiro", h: "19:00", l: "Avenida Augusto de Lima, 1715", b: "Barro Preto" },
    { n: "Bloco do Vô Manoel", d: "Sexta-feira, 13 de fevereiro", h: "19:00", l: "Rua Evaristo da Veiga, 140", b: "Senhor dos Passos" },
    { n: "Bloco Baaq de Rua", d: "Sexta-feira, 13 de fevereiro", h: "20:00", l: "Rua Marechal Deodoro, 21", b: "Floresta" },
    { n: "Bloco Fúnebre", d: "Sexta-feira, 13 de fevereiro", h: "23:00", l: "Praça da Bandeira, 49", b: "Serra" },

    // SÁBADO 14/02
    { n: "Então, Brilha!", d: "Sábado, 14 de fevereiro", h: "05:00", l: "Avenida do Contorno, 160", b: "Centro" },
    { n: "Bloco do Sustenido", d: "Sábado, 14 de fevereiro", h: "08:00", l: "Rua dos Topógrafos, 231", b: "Alípio de Melo" },
    { n: "Bloco do Silêncio", d: "Sábado, 14 de fevereiro", h: "08:00", l: "Rua Cesar Salles Barbosa, 201", b: "Mantiqueira" },
    { n: "Bloco do Pamp", d: "Sábado, 14 de fevereiro", h: "08:30", l: "Avenida Álvares Cabral, 1600", b: "Santo Agostinho" },
    { n: "Bloco do Pirulito", d: "Sábado, 14 de fevereiro", h: "09:00", l: "Rua Rubens de Souza Pimentel, 800", b: "Minaslândia" },
    { n: "Os Baterinhas", d: "Sábado, 14 de fevereiro", h: "09:00", l: "Avenida Saramenha, 1470", b: "Guarani" },
    { n: "Bloco Quando Come Se Lambuza", d: "Sábado, 14 de fevereiro", h: "09:00", l: "Avenida Afonso Pena, 532", b: "Centro" },
    { n: "Bloco do Marmiteiro", d: "Sábado, 14 de fevereiro", h: "09:00", l: "Avenida dos Esportes, 633", b: "Marmiteiros" },
    { n: "Bloco Afro Filhos das Águas", d: "Sábado, 14 de fevereiro", h: "09:30", l: "Rua Itararé, 455", b: "Concórdia" },
    { n: "Bloco Folia por Elas", d: "Sábado, 14 de fevereiro", h: "10:00", l: "Avenida Álvares Cabral, 365", b: "Lourdes" },
    { n: "Bloco Micareteiros", d: "Sábado, 14 de fevereiro", h: "10:00", l: "Avenida Getúlio Vargas, 802", b: "Savassi" },
    { n: "Uai Cê Samba", d: "Sábado, 14 de fevereiro", h: "10:00", l: "Alameda das Palmeiras, 921", b: "São José" },
    { n: "Bloco do Vô Manoel", d: "Sábado, 14 de fevereiro", h: "10:00", l: "Rua Machado de Assis, 189", b: "Senhor dos Passos" },
    { n: "Bloco do Sebá – Vai, Caboclinho!", d: "Sábado, 14 de fevereiro", h: "10:00", l: "Rua Itajubá, 273", b: "Floresta" },
    { n: "Divina Banda", d: "Sábado, 14 de fevereiro", h: "10:00", l: "Rua Marmore, 500", b: "Santa Tereza" },
    { n: "Bloco do Torresmo", d: "Sábado, 14 de fevereiro", h: "11:00", l: "Rua Marmore, 261", b: "Santa Tereza" },
    { n: "Bloco do 80", d: "Sábado, 14 de fevereiro", h: "12:00", l: "Rua Santa Rita Durão, 385", b: "Savassi" },
    { n: "Bloco Batom Rosa", d: "Sábado, 14 de fevereiro", h: "12:00", l: "Rua Cachoeira Dourada, 53", b: "Paraíso" },
    { n: "Bloco do Kanella", d: "Sábado, 14 de fevereiro", h: "12:00", l: "Rua Antônio de Albuquerque, 360", b: "Savassi" },
    { n: "Bloco Unidos de São Vicente", d: "Sábado, 14 de fevereiro", h: "12:00", l: "Rua Capitão José Carlos Vaz de Melo, 11", b: "Nova Suíssa" },
    { n: "Bloco Oh! Terezinha", d: "Sábado, 14 de fevereiro", h: "12:00", l: "Rua Divinópolis, 222", b: "Santa Tereza" },
    { n: "O Trem e os Onze", d: "Sábado, 14 de fevereiro", h: "12:00", l: "Rua Juvenal Melo Senra, 309", b: "Belvedere" },
    { n: "Deu Samba", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Rua Versília, 50", b: "Bandeirantes" },
    { n: "Bloco Gafieira", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Avenida João Pinheiro, 258", b: "Lourdes" },
    { n: "Bloco Farão", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Avenida dos Andradas, 751", b: "Centro" },
    { n: "Unidos da Estrela da Morte", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Rua Marechal Deodoro, 248", b: "Floresta" },
    { n: "Asa de Banana", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Avenida Augusto de Lima, 1845", b: "Barro Preto" },
    { n: "Bloco Xibiu", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Avenida dos Engenheiros, 600", b: "Castelo" },
    { n: "Filhas de Gaby e os Desgarrados", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Praça Coronel Benjamin Guimarães, 50", b: "Funcionários" },
    { n: "Bloco do Pirulito", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Avenida Álvares Cabral, 366", b: "Lourdes" },
    { n: "Bloco Quintal da Dona Inês", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Rua Hortência, 523", b: "Esplanada" },
    { n: "Bloco Nada Santa", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Rua Marmore, 261", b: "Santa Tereza" },
    { n: "Galera de Belô o Bloco", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Avenida Doutor Cristiano Guimarães, 2027", b: "Planalto" },
    { n: "Bloco do Magrela Feio", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Rua Itambacuri, 15", b: "Carlos Prates" },
    { n: "Aflifolia", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Rua Itapema, 174", b: "Anchieta" },
    { n: "Masterplano", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Rua Camilo Prates, 390", b: "União" },
    { n: "Micareta do Me Gusta", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Rua Nilo Aparecida Pinto, 480", b: "Planalto" },
    { n: "De Seu Bento a Dona Lúcia", d: "Sábado, 14 de fevereiro", h: "13:00", l: "Praça Arcângelo Maletta, 40", b: "Santa Lúcia" },
    { n: "Gen&All", d: "Sábado, 14 de fevereiro", h: "14:00", l: "Rua Luther King, 166", b: "Cidade Nova" },
    { n: "Eta Jurema", d: "Sábado, 14 de fevereiro", h: "14:00", l: "Rua Pernambuco, 1045", b: "Savassi" },
    { n: "Bloco do Veludo", d: "Sábado, 14 de fevereiro", h: "14:00", l: "Avenida Getúlio Vargas, 802", b: "Savassi" },
    { n: "Relembrando o Corso do Carnaval dos Anos 50", d: "Sábado, 14 de fevereiro", h: "14:00", l: "Rua Marília de Dirceu, 30", b: "Lourdes" },
    { n: "Let’s Dance", d: "Sábado, 14 de fevereiro", h: "14:00", l: "Avenida Agulhas Negras, 161", b: "Mangabeiras" },
    { n: "Baile da Sapataria", d: "Sábado, 14 de fevereiro", h: "14:00", l: "Rua Fernandes Tourinho, 588", b: "Savassi" },
    { n: "Buritis de Guimarães Rosa", d: "Sábado, 14 de fevereiro", h: "14:30", l: "Rua Henrique Badaró Portugal, 68", b: "Buritis" },
    { n: "Beagá na Folia – Clayton e Romário", d: "Sábado, 14 de fevereiro", h: "15:00", l: "Avenida Antônio Abrahão Caram, 1001", b: "São Luiz" },
    { n: "Bloco de Belô", d: "Sábado, 14 de fevereiro", h: "15:00", l: "Avenida Afonso Pena, 1377", b: "Centro" },
    { n: "Batekoo – Bloco de Carnaval", d: "Sábado, 14 de fevereiro", h: "15:00", l: "Rua dos Guajajaras, 2118", b: "Barro Preto" },
    { n: "Gato Escaldado", d: "Sábado, 14 de fevereiro", h: "15:00", l: "Avenida Afonso Pena, 4320", b: "Cruzeiro" },
    { n: "Diabas de Luneta", d: "Sábado, 14 de fevereiro", h: "15:00", l: "Rua Itajubá, 208", b: "Floresta" },
    { n: "Vem Pra Esquina Kalúar", d: "Sábado, 14 de fevereiro", h: "15:00", l: "Rua Saira, 8", b: "Goiânia" },
    { n: "Bloco do Galo", d: "Sábado, 14 de fevereiro", h: "15:00", l: "Rua Christina Maria Assis, 593", b: "Califórnia" },
    { n: "Goianeiros", d: "Sábado, 14 de fevereiro", h: "15:00", l: "Avenida Clara Nunes, 169", b: "Renascença" },
    { n: "Bloco do Vintão", d: "Sábado, 14 de fevereiro", h: "15:00", l: "Rua Uda, 362", b: "Glória" },
    { n: "Bloco Oficina Tambolelê", d: "Sábado, 14 de fevereiro", h: "16:00", l: "Rua São Cosme, 136", b: "Novo Glória" },
    { n: "As Benzedeiras", d: "Sábado, 14 de fevereiro", h: "16:00", l: "Rua João Ramalho, 122", b: "Glória" },
    { n: "Conexão African Beat", d: "Sábado, 14 de fevereiro", h: "16:00", l: "Rua Gonçalves Dias, 1364", b: "Boa Viagem" },
    { n: "Bloco da Bóta", d: "Sábado, 14 de fevereiro", h: "16:00", l: "Rua Teófilo Otoni, 591", b: "Carlos Prates" },
    { n: "Bloco Unidos do Parque Izidora", d: "Sábado, 14 de fevereiro", h: "16:00", l: "Rua Marcos Donato de Lima, 395", b: "Ribeiro de Abreu" },
    { n: "Bloco do Improviso", d: "Sábado, 14 de fevereiro", h: "16:00", l: "Rua Maestro Délio Andrade, 901", b: "Santa Efigênia" },
    { n: "Bloco Rastaxé", d: "Sábado, 14 de fevereiro", h: "17:00", l: "Rua Sete de Abril, 490", b: "Esplanada" },
    { n: "Bloco Xô Preconceito Meu Nome é Felicidade", d: "Sábado, 14 de fevereiro", h: "17:00", l: "Praça Rui Barbosa, 104", b: "Centro" },
    { n: "Baianidade Belo e Minas com Bahia", d: "Sábado, 14 de fevereiro", h: "17:00", l: "Avenida dos Engenheiros, 578", b: "Castelo" },
    { n: "Bloco Anjos do Céu", d: "Sábado, 14 de fevereiro", h: "17:30", l: "Rua Antônio Giarola, 51", b: "Céu Azul" },

    // DOMINGO 15/02
    { n: "Bloco do Corre! Carnaval + Corrida", d: "Domingo, 15 de fevereiro", h: "07:00", l: "Alameda das Palmeiras, 847", b: "São José" },
    { n: "Pena de Pavão de Krishna", d: "Domingo, 15 de fevereiro", h: "08:00", l: "Rua João das Chagas, 42", b: "União" },
    { n: "Bloco de PiFanos BH", d: "Domingo, 15 de fevereiro", h: "08:00", l: "Rua Marmore, 157", b: "Santa Tereza" },
    { n: "Lagum na Avenida", d: "Domingo, 15 de fevereiro", h: "08:00", l: "Rua Santa Rita Durão, 1216", b: "Savassi" },
    { n: "Todas as Mulheres do Brasil", d: "Domingo, 15 de fevereiro", h: "08:00", l: "Avenida Sinfronio Brochado, 1170", b: "Barreiro" },
    { n: "Bloco da Martinha", d: "Domingo, 15 de fevereiro", h: "08:30", l: "Rua dos Economistas, 411", b: "Alípio de Melo" },
    { n: "Bloco Cilada", d: "Domingo, 15 de fevereiro", h: "09:00", l: "Rua Manoel dos Reis, 70", b: "Jaraguá" },
    { n: "Bloco Filhos da PUC MG", d: "Domingo, 15 de fevereiro", h: "09:00", l: "Avenida Getúlio Vargas, 820", b: "Savassi" },
    { n: "Os Bateras", d: "Domingo, 15 de fevereiro", h: "09:00", l: "Avenida Saramenha, 1432", b: "Guarani" },
    { n: "99 Não é 100", d: "Domingo, 15 de fevereiro", h: "09:00", l: "Avenida Augusto de Lima, 1845", b: "Barro Preto" },
    { n: "Bloco Pacato Cidadão", d: "Domingo, 15 de fevereiro", h: "09:00", l: "Avenida dos Bandeirantes, 240", b: "Sion" },
    { n: "Associação Cultural Arautos do Gueto", d: "Domingo, 15 de fevereiro", h: "09:00", l: "Avenida Silva Lobo, 1336", b: "Nova Granada" },
    { n: "Brócolis", d: "Domingo, 15 de fevereiro", h: "09:00", l: "Rua Bragança, 3", b: "Coqueiros" },
    { n: "Pé de Feijão", d: "Domingo, 15 de fevereiro", h: "09:00", l: "Rua Ibitinga, 7", b: "Concórdia" },
    { n: "Raga Mofe", d: "Domingo, 15 de fevereiro", h: "09:00", l: "Rua Frei Orlando, 1023", b: "Caiçaras" },
    { n: "Maçã do Amor", d: "Domingo, 15 de fevereiro", h: "09:00", l: "Rua Fernandes Tourinho, 515", b: "Savassi" },
    { n: "Bloco Afro-Periférico Orisamba", d: "Domingo, 15 de fevereiro", h: "10:00", l: "Rua Itapecerica, 321", b: "Lagoinha" },
    { n: "Bloco da Anistia (antigo Bloco da Papuda)", d: "Domingo, 15 de fevereiro", h: "10:00", l: "Rua Curitiba, 2457", b: "Lourdes" },
    { n: "Bloco Românticos São Loucos", d: "Domingo, 15 de fevereiro", h: "10:00", l: "Rua Marmore, 189", b: "Santa Tereza" },
    { n: "Bloco do Batiza", d: "Domingo, 15 de fevereiro", h: "10:00", l: "Rua Mucuri, 331", b: "Floresta" },
    { n: "Todo Mundo Cabe no Mundo", d: "Domingo, 15 de fevereiro", h: "10:00", l: "Rua Piauí, 644", b: "Santa Efigênia" },
    { n: "Bloco Galo Buritis", d: "Domingo, 15 de fevereiro", h: "11:00", l: "Rua Henrique Badaró Portugal, 143", b: "Buritis" },
    { n: "Cenário Kids", d: "Domingo, 15 de fevereiro", h: "11:00", l: "Rua Michel Jeha, 16", b: "São Bento" },
    { n: "Bloco da Juju Pauline", d: "Domingo, 15 de fevereiro", h: "12:00", l: "Avenida Fleming, 710", b: "Ouro Preto" },
    { n: "Baianeiros", d: "Domingo, 15 de fevereiro", h: "12:00", l: "Avenida Altamiro Avelino Soares, 78", b: "Castelo" },
    { n: "Bloco Uruntú – O Som dos Povos", d: "Domingo, 15 de fevereiro", h: "12:00", l: "Rua Cecilídes Moreira de Faria, 100", b: "Nova Gameleira" },
    { n: "Bloco de Rua Inocentes de Santa Tereza", d: "Domingo, 15 de fevereiro", h: "12:00", l: "Rua Mármore, 169", b: "Santa Tereza" },
    { n: "Bloquinho Mais Amor", d: "Domingo, 15 de fevereiro", h: "12:00", l: "Avenida Afonso Pena, 4405", b: "Serra" },
    { n: "Bloquim Dubem", d: "Domingo, 15 de fevereiro", h: "12:00", l: "Rua Dep. Bernardino de Sena Figueiredo, 1022", b: "Cidade Nova" },
    { n: "Unidos do Samba Queixinho", d: "Domingo, 15 de fevereiro", h: "12:30", l: "Rua da Bahia, 2178", b: "Lourdes" },
    { n: "Fofoca de Carimbó", d: "Domingo, 15 de fevereiro", h: "13:00", l: "Avenida dos Andradas, 800", b: "Centro" },
    { n: "Bloco Samba de Catarina", d: "Domingo, 15 de fevereiro", h: "13:00", l: "Avenida Sinfronio Brochado, 1170", b: "Barreiro" },
    { n: "Beagá na Folia – Desmantelo do Nathan", d: "Domingo, 15 de fevereiro", h: "13:00", l: "Avenida Afonso Pena, 792", b: "Centro" },
    { n: "Bloco Marinada", d: "Domingo, 15 de fevereiro", h: "13:00", l: "Avenida Antonio Abrahão Caram, 1001", b: "São Luiz" },
    { n: "Abre Que Tô Passanu", d: "Domingo, 15 de fevereiro", h: "13:00", l: "Praça Marino Mendes Campos, 1078", b: "Anchieta" },
    { n: "Bloco Show", d: "Domingo, 15 de fevereiro", h: "13:00", l: "Praça da Liberdade, 200", b: "Savassi" },
    { n: "Bloco dos Super Heróis", d: "Domingo, 15 de fevereiro", h: "13:00", l: "Rua Araguari, 1561", b: "Santo Agostinho" },
    { n: "Tico Tico Serra Copo", d: "Domingo, 15 de fevereiro", h: "13:00", l: "Rua Euro Luis Arantes, 126", b: "Novo Aarão Reis" },
    { n: "Bloco do Jiló", d: "Domingo, 15 de fevereiro", h: "13:00", l: "Rua Contagem, 823", b: "Santa Inês" },
    { n: "Bloco Afrodíaspora", d: "Domingo, 15 de fevereiro", h: "13:00", l: "Rua dos Guajajaras, 1446", b: "Barro Preto" },
    { n: "Kola em Mim Que Cê Brilha", d: "Domingo, 15 de fevereiro", h: "13:30", l: "Rua Oeste, 62", b: "Calafate" },
    { n: "Bloco Demorô", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Avenida Magenta, 571", b: "Vitória" },
    { n: "Carna15", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Rua Jornalista Geraldo Resende, 474", b: "Serrano" },
    { n: "Bloco da Graça", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Rua Silveira, 530", b: "Graça" },
    { n: "Bloco du Coco", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Rua Belmiro Braga, 1020", b: "Caiçara-Adelaide" },
    { n: "Bloco do Bigode", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Avenida Américo Vespúcio, 965", b: "Aparecida" },
    { n: "Bloco Amigos do Bazin", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Praça Pedro Celso Abreu, 1053", b: "Dona Clara" },
    { n: "Folia do Tavinho", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Rua Alberto Cintra, 47", b: "União" },
    { n: "Bloco do Mamá", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Rua Lídio Lunardi, 83", b: "Maria Virgínia" },
    { n: "Bloco Fervêção", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Rua Sergipe, 1199", b: "Savassi" },
    { n: "Bloco Vou Ali e Volto", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Praça Capela Nova, 318", b: "Padre Eustáquio" },
    { n: "Carnaleste", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Rua Silva Alvarenga, 583", b: "São Geraldo" },
    { n: "Baile da Serra", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Avenida Mem de Sá, 1833", b: "Fazendinha" },
    { n: "É o Amô", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Avenida dos Andradas, 3560", b: "Pompeia" },
    { n: "As Grandes Figuras", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Rua Joaquim Zenir Leite, 431", b: "Paraíso" },
    { n: "Jângalove", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Rua Outono, 559", b: "Cruzeiro" },
    { n: "Afoxé Ilê Odara", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Rua São Clemente, 1132", b: "Aparecida" },
    { n: "Furacão Trembase", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Avenida Assis Chateaubriand, 77", b: "Floresta" },
    { n: "Afoxé Bandarerê", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Praça México, 211", b: "Concórdia" },
    { n: "Trem Bão D+", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Rua Espírito Santo, 1676", b: "Lourdes" },
    { n: "Bloco da Alcova Libertina", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Rua Camilo Prates, 390", b: "União" },
    { n: "Bloco da Saúde", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Rua Mármore, 161", b: "Santa Tereza" },
    { n: "Bloco Oncetá", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Avenida General Olímpio Mourão Filho, 750", b: "Itapoã" },
    { n: "Bloco Micareteiros", d: "Domingo, 15 de fevereiro", h: "14:00", l: "Avenida Getúlio Vargas, 800", b: "Savassi" },
    { n: "Bloco Avulsa", d: "Domingo, 15 de fevereiro", h: "15:00", l: "Rua Ouro Preto, 368", b: "Barro Preto" },
    { n: "Festa de Crente", d: "Domingo, 15 de fevereiro", h: "15:00", l: "Avenida Gastão Demétrio Maia, 2201", b: "Floramar" },
    { n: "Bloco Pipixo", d: "Domingo, 15 de fevereiro", h: "15:00", l: "Avenida Saramenha, 1923", b: "Guarani" },
    { n: "Bloco Quem Eu Quero Não Me Quer", d: "Domingo, 15 de fevereiro", h: "15:00", l: "Rua Senador Lima Guimarães, 251", b: "Estoril" },
    { n: "Eleganza", d: "Domingo, 15 de fevereiro", h: "15:00", l: "Avenida Augusto de Lima, 1845", b: "Barro Preto" },
    { n: "Carnavovô", d: "Domingo, 15 de fevereiro", h: "15:00", l: "Rua Tenente Marino Freire, 270", b: "Maria Helena" },
    { n: "Tropa do 7LC", d: "Domingo, 15 de fevereiro", h: "15:00", l: "Rua Elísio de Brito, 760", b: "São Geraldo" },
    { n: "Vejo Flores em Você", d: "Domingo, 15 de fevereiro", h: "15:00", l: "Rua Jorge Antonio Nassar, 289", b: "Letícia" },
    { n: "Unidos da Beira Linha", d: "Domingo, 15 de fevereiro", h: "16:00", l: "Rua Antonio Mariano de Abreu, 240", b: "Paulo VI" },
    { n: "Chic Xique Forrozeirros", d: "Domingo, 15 de fevereiro", h: "16:00", l: "Rua Versília, 50", b: "Bandeirantes" },
    { n: "Bloco da Insanidade", d: "Domingo, 15 de fevereiro", h: "16:00", l: "Avenida Afonso Pena, 4406", b: "Cruzeiro" },
    { n: "VemKavê", d: "Domingo, 15 de fevereiro", h: "16:00", l: "Rua Inês Glansman, 168", b: "Ribeiro de Abreu" },
    { n: "Bloco da Língua", d: "Domingo, 15 de fevereiro", h: "16:00", l: "Rua Tigre, 8", b: "São Salvador" },
    { n: "Amigos da PPL BH", d: "Domingo, 15 de fevereiro", h: "16:00", l: "Rua Popular, 26", b: "Aparecida" },
    { n: "Bloco Tô de Altas", d: "Domingo, 15 de fevereiro", h: "17:00", l: "Avenida Fleming, 1200", b: "Ouro Preto" },
    { n: "Os Meninos da Meia-Noite", d: "Domingo, 15 de fevereiro", h: "17:00", l: "Praça Santa Rita, 130", b: "Esplanada" },

    // SEGUNDA 16/02
    { n: "Samba do Arco", d: "Segunda-feira, 16 de fevereiro", h: "08:00", l: "Praça México, 211", b: "Concórdia" },
    { n: "Bloco Maldito", d: "Segunda-feira, 16 de fevereiro", h: "08:00", l: "Rua David Campista, 42", b: "Floresta" },
    { n: "Baianas Ozadas", d: "Segunda-feira, 16 de fevereiro", h: "08:00", l: "Avenida Afonso Pena, 996", b: "Centro" },
    { n: "Bloco da Anninha", d: "Segunda-feira, 16 de fevereiro", h: "09:00", l: "Avenida Altamiro Avelino Soares, 110", b: "Castelo" },
    { n: "Meninos do Morro", d: "Segunda-feira, 16 de fevereiro", h: "09:00", l: "Rua Mármore, 184", b: "Santa Tereza" },
    { n: "Bloco Filhos da PUC MG", d: "Segunda-feira, 16 de fevereiro", h: "09:00", l: "Avenida Afonso Pena, 1620", b: "Boa Viagem" },
    { n: "Bloco Corte Devassa", d: "Segunda-feira, 16 de fevereiro", h: "09:00", l: "Rua dos Tabaiares, 84", b: "Floresta" },
    { n: "Pão de Queijo – Xodó de Minas", d: "Segunda-feira, 16 de fevereiro", h: "09:00", l: "Avenida Olegário Maciel, 1704", b: "Santo Agostinho" },
    { n: "Resenha Amigos do Edgar", d: "Segunda-feira, 16 de fevereiro", h: "09:00", l: "Praça Santa Rita, 500", b: "Esplanada" },
    { n: "Daquele Jeito", d: "Segunda-feira, 16 de fevereiro", h: "09:00", l: "Rua da Bahia, 1764", b: "Lourdes" },
    { n: "Bloco Xibuzinho", d: "Segunda-feira, 16 de fevereiro", h: "09:30", l: "Avenida dos Engenheiros, 295", b: "Alípio de Melo" },
    { n: "EBBloco", d: "Segunda-feira, 16 de fevereiro", h: "09:30", l: "Rua Vicente Risola, 33", b: "Santa Inês" },
    { n: "Beagá na Folia – Sertanejinho do Telô", d: "Segunda-feira, 16 de fevereiro", h: "10:00", l: "Avenida Antonio Abrahão Caram, 1001", b: "São Luiz" },
    { n: "Bloco Belorinho", d: "Segunda-feira, 16 de fevereiro", h: "10:00", l: "Avenida Getúlio Vargas, 1503", b: "Savassi" },
    { n: "Bloco da Bia", d: "Segunda-feira, 16 de fevereiro", h: "10:00", l: "Avenida Guarapari, 1325", b: "Santa Amélia" },
    { n: "Unidos do Barro Preto", d: "Segunda-feira, 16 de fevereiro", h: "10:00", l: "Avenida Augusto de Lima, 1826", b: "Barro Preto" },
    { n: "Bloco Não Acredito Que Te Beijei", d: "Segunda-feira, 16 de fevereiro", h: "10:30", l: "Não informado", b: "Diversos" },
    { n: "Bloco Rei", d: "Segunda-feira, 16 de fevereiro", h: "10:30", l: "Rua Goiás, 41", b: "Centro" },
    { n: "Bom Bloquiu", d: "Segunda-feira, 16 de fevereiro", h: "11:00", l: "Praça da Bandeira, 188", b: "Comiteco" },
    { n: "Apucabloco", d: "Segunda-feira, 16 de fevereiro", h: "11:00", l: "Avenida Dom José Gaspar, 599", b: "Coração Eucarístico" },
    { n: "Mindinho Bateria Mirim", d: "Segunda-feira, 16 de fevereiro", h: "11:00", l: "Rua Mármore, 169", b: "Santa Tereza" },
    { n: "Altas Horas o Bloco", d: "Segunda-feira, 16 de fevereiro", h: "11:00", l: "Rua Ilacir Pereira Lima, 181", b: "Silveira" },
    { n: "Bloco Unidos de São Vicente", d: "Segunda-feira, 16 de fevereiro", h: "12:00", l: "Praça Paulo Sigaud, 65", b: "Calafate" },
    { n: "Bloco Grupo 80&90 BHZ", d: "Segunda-feira, 16 de fevereiro", h: "12:00", l: "Avenida dos Clarins, 48", b: "Conjunto Califórnia I" },
    { n: "Afoxé Erês", d: "Segunda-feira, 16 de fevereiro", h: "12:00", l: "Rua Cerqueira Leite, 18", b: "Universitário" },
    { n: "Bloco Afro Filhos de Afonjá", d: "Segunda-feira, 16 de fevereiro", h: "12:00", l: "Rua Antonio Gentil, 230", b: "Concórdia" },
    { n: "Katope", d: "Segunda-feira, 16 de fevereiro", h: "12:00", l: "Avenida Artur Guimarães, 443", b: "Santa Cruz" },
    { n: "Bloco Alalaor", d: "Segunda-feira, 16 de fevereiro", h: "12:00", l: "Rua Silvianópolis, 231", b: "Santa Tereza" },
    { n: "Chama Que Vem", d: "Segunda-feira, 16 de fevereiro", h: "12:00", l: "Praça Padre Lage, 76", b: "Heliópolis" },
    { n: "Bloco Trem na Cabeça", d: "Segunda-feira, 16 de fevereiro", h: "12:00", l: "Avenida Bernardo Monteiro, 1561", b: "Funcionários" },
    { n: "Bloco Samba Cana", d: "Segunda-feira, 16 de fevereiro", h: "13:00", l: "Avenida Silva Lobo, 1748", b: "Nova Granada" },
    { n: "Me Segura Se Não Eu Caio", d: "Segunda-feira, 16 de fevereiro", h: "13:00", l: "Rua Garoa Parar, 759", b: "Santa Efigênia" },
    { n: "Maria Pretinha", d: "Segunda-feira, 16 de fevereiro", h: "13:00", l: "Avenida Doutor Cristiano Guimarães, 2005", b: "Planalto" },
    { n: "Bloco Gandahia", d: "Segunda-feira, 16 de fevereiro", h: "13:00", l: "Rua Moisés Kalli, 300", b: "Buritis" },
    { n: "Bloco Seu Brás", d: "Segunda-feira, 16 de fevereiro", h: "13:00", l: "Rua Braz Cubas, 137", b: "Anchieta" },
    { n: "Bloco da Cinara", d: "Segunda-feira, 16 de fevereiro", h: "13:00", l: "Rua Mármore, 157", b: "Santa Tereza" },
    { n: "Bloco da Cíclia", d: "Segunda-feira, 16 de fevereiro", h: "13:30", l: "Rua Oeste, 425", b: "Calafate" },
    { n: "Bloco da Pri", d: "Segunda-feira, 16 de fevereiro", h: "14:00", l: "Praça México, 249", b: "Concórdia" },
    { n: "Corpo Indocente", d: "Segunda-feira, 16 de fevereiro", h: "14:00", l: "Rua Gonçalves Dias, 1400", b: "Lourdes" },
    { n: "Bloco do Pé Vermei", d: "Segunda-feira, 16 de fevereiro", h: "14:00", l: "Avenida Bueno Siqueira, 21", b: "Universitário" },
    { n: "Saiba Viver", d: "Segunda-feira, 16 de fevereiro", h: "14:00", l: "Avenida João Pinheiro, 277", b: "Boa Viagem" },
    { n: "Bloco Me Deixe", d: "Segunda-feira, 16 de fevereiro", h: "14:00", l: "Avenida Getúlio Vargas, 1509", b: "Savassi" },
    { n: "Bloco Circulou BH", d: "Segunda-feira, 16 de fevereiro", h: "14:00", l: "Avenida dos Engenheiros, 690", b: "Castelo" },
    { n: "Reven Beats", d: "Segunda-feira, 16 de fevereiro", h: "14:00", l: "Avenida Agulhas Negras, 161", b: "Mangabeiras" },
    { n: "Bloco Uai Sô!", d: "Segunda-feira, 16 de fevereiro", h: "14:00", l: "Avenida Guarapari, 1301", b: "Santa Amélia" },
    { n: "Amigos da Rua do Córrego", d: "Segunda-feira, 16 de fevereiro", h: "14:00", l: "Rua Viçosa, 572", b: "Santo Antônio" },
    { n: "Bloco Noise Music", d: "Segunda-feira, 16 de fevereiro", h: "15:00", l: "Rua Mucuri, 313", b: "Floresta" },
    { n: "Bloco And Roll", d: "Segunda-feira, 16 de fevereiro", h: "15:00", l: "Rua dos Guajajaras, 2126", b: "Barro Preto" },
    { n: "Tropa do Serrão", d: "Segunda-feira, 16 de fevereiro", h: "15:00", l: "Rua Antonio Christiano, 685", b: "União" },
    { n: "Bloco Arrastão Eletrônico", d: "Segunda-feira, 16 de fevereiro", h: "15:00", l: "Avenida Assis Chateaubriand, 77", b: "Floresta" },
    { n: "Bloco Unidos do Ribeirão do Onça", d: "Segunda-feira, 16 de fevereiro", h: "15:00", l: "Rua Euro Luis Arantes, 174", b: "Novo Aarão Reis" },
    { n: "Sou Vermelho", d: "Segunda-feira, 16 de fevereiro", h: "15:00", l: "Avenida Alvares Cabral, 400", b: "Lourdes" },
    { n: "Bloco Growth", d: "Segunda-feira, 16 de fevereiro", h: "15:00", l: "Rua Cesario Alvim, 398", b: "Padre Eustáquio" },
    { n: "Latinobregarave", d: "Segunda-feira, 16 de fevereiro", h: "15:00", l: "Praça Comendador Negrão de Lima, 159", b: "Floresta" },
    { n: "Beagá na Folia – Bloco do Zé", d: "Segunda-feira, 16 de fevereiro", h: "15:00", l: "Avenida Antonio Abrahão Caram, 1001", b: "São Luiz" },
    { n: "Madagascar", d: "Segunda-feira, 16 de fevereiro", h: "15:00", l: "Rua Marechal Deodoro, 122", b: "Floresta" },
    { n: "Bloco da Horny", d: "Segunda-feira, 16 de fevereiro", h: "15:00", l: "Avenida Augusto de Lima, 1845", b: "Barro Preto" },
    { n: "Bloco Axé Retrô do Marão", d: "Segunda-feira, 16 de fevereiro", h: "15:00", l: "Rua Pedro Sigaud, 390", b: "Grajaú" },
    { n: "Balança BH", d: "Segunda-feira, 16 de fevereiro", h: "16:00", l: "Rua Jacareí, 144", b: "Pindorama" },
    { n: "Bloco Love/Paranoia", d: "Segunda-feira, 16 de fevereiro", h: "16:00", l: "Rua Rodrigues Caldas, 131", b: "Santo Agostinho" },
    { n: "O Leão da Lagoinha", d: "Segunda-feira, 16 de fevereiro", h: "16:00", l: "Rua Itapecerica, 878", b: "Lagoinha" },
    { n: "VemKavê", d: "Segunda-feira, 16 de fevereiro", h: "16:00", l: "Rua Inês Glansman, 15", b: "Ribeiro de Abreu" },
    { n: "Bloco Rastaxé", d: "Segunda-feira, 16 de fevereiro", h: "17:00", l: "Rua Sete de Abril, 485", b: "Esplanada" },

    // TERÇA 17/02
    { n: "Tiozões do Pagode", d: "Terça-feira, 17 de fevereiro", h: "07:30", l: "Rua Oeste, 510", b: "Calafate" },
    { n: "Juventude Bronzeada", d: "Terça-feira, 17 de fevereiro", h: "08:00", l: "Avenida Assis Chateaubriand, 77", b: "Floresta" },
    { n: "Truck do Desejo", d: "Terça-feira, 17 de fevereiro", h: "08:00", l: "Rua Prof. Amaro Xisto de Queiroz, 643", b: "União" },
    { n: "Bloco Magnólia", d: "Terça-feira, 17 de fevereiro", h: "08:00", l: "Rua Magnólia, 675", b: "Caiçaras" },
    { n: "Bloco Fora da Cena", d: "Terça-feira, 17 de fevereiro", h: "09:00", l: "Rua Sergipe, 811", b: "Savassi" },
    { n: "Bloco Putz Grilla", d: "Terça-feira, 17 de fevereiro", h: "09:00", l: "Avenida Getúlio Vargas, 809", b: "Savassi" },
    { n: "Bora Pro Nobis", d: "Terça-feira, 17 de fevereiro", h: "09:00", l: "Avenida Afonso Pena, 4360", b: "Cruzeiro" },
    { n: "Bloco Me Deixe", d: "Terça-feira, 17 de fevereiro", h: "09:00", l: "Avenida Afonso Pena, 1377", b: "Centro" },
    { n: "Fita Amarela", d: "Terça-feira, 17 de fevereiro", h: "09:00", l: "Avenida José Cândido da Silveira, 1500", b: "Horto Florestal" },
    { n: "Coco da Gente", d: "Terça-feira, 17 de fevereiro", h: "09:00", l: "Rua Quartzo, 12", b: "Vila Dias" },
    { n: "Banho de Xêro", d: "Terça-feira, 17 de fevereiro", h: "09:00", l: "Avenida Guarapari, 643", b: "Santa Amélia" },
    { n: "Bloco do Odilara", d: "Terça-feira, 17 de fevereiro", h: "09:00", l: "Rua Marmore, 178", b: "Santa Tereza" },
    { n: "Bloco Passinho Pelo Mundo", d: "Terça-feira, 17 de fevereiro", h: "10:00", l: "Avenida dos Andradas, 871", b: "Centro" },
    { n: "Carna Flora", d: "Terça-feira, 17 de fevereiro", h: "11:00", l: "Rua José Soares, 147", b: "Floramar" },
    { n: "BH É Doidi Mais", d: "Terça-feira, 17 de fevereiro", h: "11:00", l: "Rua Wiver Hernandes da Silva, 230", b: "Manacás" },
    { n: "Bloco Esperando o Metrô", d: "Terça-feira, 17 de fevereiro", h: "11:00", l: "Rua Barão de Coromandel, 790", b: "Barreiro" },
    { n: "Chega o Rei", d: "Terça-feira, 17 de fevereiro", h: "11:00", l: "Rua Frei Conceição Veloso, 308", b: "João Pinheiro" },
    { n: "Maria Baderna", d: "Terça-feira, 17 de fevereiro", h: "12:00", l: "Rua Mármore, 295", b: "Santa Tereza" },
    { n: "Bloco Elis Ideal", d: "Terça-feira, 17 de fevereiro", h: "13:00", l: "Rua Eli Seabra Filho, 579", b: "Buritis" },
    { n: "Bloco Du Sô Tchê", d: "Terça-feira, 17 de fevereiro", h: "13:00", l: "Rua Manoel Alexandrino, 426", b: "São Paulo" },
    { n: "Me Ampare Que Eu Toco", d: "Terça-feira, 17 de fevereiro", h: "13:00", l: "Rua Antônio Justino, 98", b: "Pompeia" },
    { n: "Bloco do Jiló", d: "Terça-feira, 17 de fevereiro", h: "13:00", l: "Rua Contagem, 766", b: "Boa Vista" },
    { n: "Samba D’Ouro", d: "Terça-feira, 17 de fevereiro", h: "13:00", l: "Praça México, 96", b: "Concórdia" },
    { n: "Kola em Mim Que Cê Brilha", d: "Terça-feira, 17 de fevereiro", h: "13:30", l: "Rua Oeste, 526", b: "Calafate" },
    { n: "Lavô, Tá Novo!", d: "Terça-feira, 17 de fevereiro", h: "14:00", l: "Avenida dos Andradas, 3560", b: "Pompeia" },
    { n: "Bloco do Fantasminha", d: "Terça-feira, 17 de fevereiro", h: "14:00", l: "Avenida Mem de Sá, 1823", b: "Fazendinha" },
    { n: "Bloco Kebrae", d: "Terça-feira, 17 de fevereiro", h: "14:00", l: "Avenida Assis Chateaubriand, 115", b: "Floresta" },
    { n: "Bloco Tá Mole Mas É Meu", d: "Terça-feira, 17 de fevereiro", h: "14:00", l: "Rua Dom Oscar Romero, 224", b: "Nova Gameleira" },
    { n: "Bloco da Guitarrinha", d: "Terça-feira, 17 de fevereiro", h: "14:00", l: "Rua Itaquera, 1166", b: "Graça" },
    { n: "Mulatos da Bela Vista", d: "Terça-feira, 17 de fevereiro", h: "14:00", l: "Rua Bela Vista, 10", b: "Padre Eustáquio" },
    { n: "Bloco da Bruna Lipiani", d: "Terça-feira, 17 de fevereiro", h: "14:00", l: "Avenida Altamiro Avelino Soares, 233", b: "Castelo" },
    { n: "Me Beija Que Eu Sou Pagodeiro", d: "Terça-feira, 17 de fevereiro", h: "15:00", l: "Avenida Afonso Pena, 866", b: "Centro" },
    { n: "Baque de Mina", d: "Terça-feira, 17 de fevereiro", h: "15:00", l: "Avenida Álvares Cabral, 365", b: "Lourdes" },
    { n: "SBC Samba, Bobagem e Cerveja", d: "Terça-feira, 17 de fevereiro", h: "15:00", l: "Rua Vila Rica, 1471", b: "Jardim Montanhês" },
    { n: "Bloco do Carlão", d: "Terça-feira, 17 de fevereiro", h: "15:30", l: "Rua Oeste, 650", b: "Calafate" },
    { n: "Mientras Dura", d: "Terça-feira, 17 de fevereiro", h: "16:00", l: "Avenida Artur Guimarães, 1290", b: "Santa Cruz" },
    { n: "Bloco Pisa na Fulô", d: "Terça-feira, 17 de fevereiro", h: "16:00", l: "Rua Prof. Amaro Xisto de Queiroz, 643", b: "União" },
    { n: "Bloco Enche Meu Copo", d: "Terça-feira, 17 de fevereiro", h: "16:00", l: "Rua Vinte e Oito de Setembro, 801", b: "Esplanada" },
    { n: "Sambô São Salvador", d: "Terça-feira, 17 de fevereiro", h: "16:00", l: "Rua Monte Verde, 364", b: "São Salvador" },
    { n: "Nega Biruta", d: "Terça-feira, 17 de fevereiro", h: "16:00", l: "Alameda Aracari, 5", b: "Dom Cabral" },
    { n: "Bloco Axé das Antigas", d: "Terça-feira, 17 de fevereiro", h: "16:00", l: "Avenida Antônio Abrahão Caram, 996", b: "São José" },
    { n: "Bloco Açaí Guardiã", d: "Terça-feira, 17 de fevereiro", h: "17:30", l: "Rua Jequiriça, 81", b: "Concórdia" },

    // QUARTA 18/02
    { n: "Bloco Lua de Crixtal", d: "Quarta-feira, 18 de fevereiro", h: "10:00", l: "Avenida Francisco Sales, 20", b: "Floresta" },
    { n: "Bloco Afro Magia Negra", d: "Quarta-feira, 18 de fevereiro", h: "12:00", l: "Rua Itararé, 96", b: "Concórdia" },
    { n: "Babadan Banda de Rua", d: "Quarta-feira, 18 de fevereiro", h: "14:00", l: "Avenida José Bonifácio, 229", b: "Pedreira Prado Lopes" },
    { n: "Bloco I Wanna Love You", d: "Quarta-feira, 18 de fevereiro", h: "14:00", l: "Rua Dona Maria Ignez, 110", b: "Floresta" },
    { n: "Bloco da Saudade", d: "Quarta-feira, 18 de fevereiro", h: "15:30", l: "Rua Arcos, 762", b: "Saudade" },
    { n: "Ponto Nordeste", d: "Quarta-feira, 18 de fevereiro", h: "16:00", l: "Rua Aarão Reis, 570", b: "Centro" },
    { n: "Bloco Arrasta Bloco de Favela", d: "Quarta-feira, 18 de fevereiro", h: "18:00", l: "Rua Wiver Hernandes da Silva, 126", b: "Manacás" }
];

function inicializar() {
    // Gerar bairros únicos e ordenar
    const bairros = [...new Set(blocos.map(b => b.b))].sort();
    const selectBairro = document.getElementById('filtro-bairro');
    bairros.forEach(b => {
        const opt = document.createElement('option');
        opt.value = b;
        opt.textContent = b;
        selectBairro.appendChild(opt);
    });

    // Eventos de mudança nos selects e input
    document.querySelectorAll('select').forEach(el => el.addEventListener('change', filtrar));
    document.getElementById('filtro-nome').addEventListener('input', filtrar);

    filtrar();
}

function filtrar() {
    const nomeBusca = document.getElementById('filtro-nome').value.toLowerCase();
    const diaSel = document.getElementById('filtro-dia').value;
    const bairroSel = document.getElementById('filtro-bairro').value;
    const turnoSel = document.getElementById('filtro-horario').value;
    const container = document.getElementById('lista-blocos');
    const contador = document.getElementById('contador');

    const filtrados = blocos.filter(b => {
        const matchNome = b.n.toLowerCase().includes(nomeBusca);
        const matchDia = !diaSel || b.d === diaSel;
        const matchBairro = !bairroSel || b.b === bairroSel;
        
        const horaH = parseInt(b.h.replace('h', '').split(':')[0]);
        let matchTurno = true;
        if (turnoSel === 'manha') matchTurno = horaH < 12;
        else if (turnoSel === 'tarde') matchTurno = horaH >= 12 && horaH < 18;
        else if (turnoSel === 'noite') matchTurno = horaH >= 18;

        return matchNome && matchDia && matchBairro && matchTurno;
    });

    contador.textContent = `${filtrados.length} blocos encontrados`;
    container.innerHTML = filtrados.length ? '' : '<p class="vazio">Ops! Nenhum bloco encontrado com esses filtros.</p>';

    filtrados.forEach(b => {
        const card = document.createElement('div');
        card.className = 'bloco-card';
        card.innerHTML = `
            <div class="tag-dia">${b.d}</div>
            <div class="bloco-nome">${b.n}</div>
            <div class="bloco-info">
                <strong>🕒 Horário:</strong> ${b.h}<br>
                <strong>📍 Local:</strong> ${b.l} - ${b.b}
            </div>
        `;
        container.appendChild(card);
    });
}

window.onload = inicializar;
</script>

</body>
</html>
