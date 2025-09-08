-- Configurações e lista de Brainrots organizados por categoria
local brainrots = {
    ["Common"] = {
        "Noobini Pizzanini", "Lirili Larila", "Tim Cheese", "FluriFlura", "Talpa Di Fero",
        "Svinina Bombardino", "Pipi Kiwi", "Pipi Corni"
    },
    ["Rare"] = {
        "Trippi Troppi", "Tung Tung Tung Sahur", "Gangster Footera", "Boneca Ambalabu",
        "Ta Ta Ta Ta Sahur", "Tric Trac Baraboom", "Bandito Bobritto", "Cacto Hipopotamo", "Pipi Avocado"
    },
    ["Epic"] = {
        "Cappuccino Assassino", "Trulimero Trulicina", "Bambini Crostini", "Bananita Dolphinita",
        "Brri Brri Bicus Dicus Bombicus", "Brr Brr Patapim", "Perochello Lemonchello",
        "Avocadini Guffo", "Salamino Penguino", "Ti Ti Ti Sahur", "Penguino Cocosino", 
        "Avocadini Antilopini", "Bandiro Axolito", "Malame Amarele", "Mangolini Parrocini"
    },
    ["Legendary"] = {
        "Burbaloni Loliloli", "Chimpazini Bananini", "Ballerina Cappuccina", "Chef Crabracadabra",
        "Glorbo Fruttodrillo", "Blueberrini Octopusini", "Lionel Cactuseli", "Pandaccini Bananini",
        "Strawberelli Flamingelli", "Sigma Boy", "Cocosini Mama", "Pi Pi Watermelon", "Pipi Potato",
        "Quivioli Ameleonni", "Tirilikalika Tirilikalako", "Caramello Filtrello", "Signore Carapace"
    },
    ["Mythic"] = {
        "Frigo Camelo", "Orangutini Ananassini", "Rhino Toasterino", "Bombardiro Crocodilo",
        "Bombombini Gusini", "Cavallo Virtuso", "Spioniro Golubiro", "Tigrillini Watermelini",
        "Zibra Zubra Zibralini", "Gorillo Watermelondrillo", "Avocadorilla", "Ganganzelli Trulala",
        "Tob Tobi Tobi", "Te Te Te Sahur", "Tracoducotulu Delapeladustuz", "Lerulerulerule",
        "Carrotini Brainini", "Gorillo Subwoofero", "Bruto Gialutto", "Los Noobinis"
    },
    ["God Tier"] = {
        "Cocofanto Elefanto", "Girafa Celestre", "Tralalero Tralala", "Odin Din Din Dun",
        "Trenostruzzo Turbo 3000", "Ballerino Lololo", "Los Crocodillitos", "Orcalero Orcala",
        "Tigroligre Frutonni", "Piccione Macchina", "Los Tungtungtungcitos", "Tukanno Bananno",
        "Trippi Troppi Troppa Trippa", "Bulbito Bandito Traktorito", "Los Orcalitos",
        "Tralalita Tralala", "Pakrahmatmamat", "Tartaruga Cisterna", "Brr es Teh Patipum",
        "Cacasito Satalito", "Mastodontico Telepiedone", "Gattito Tacoto", "Chihuanini Taconini",
        "Los Tipi Tacos", "Anpali Babel", "Las Cappucinas", "Orcalita Orcala",
        "Piccionetta Machina", "Antonio"
    },
    ["Secret"] = {
        "La Vacca Staturno Saturnita", "Los Tralaleritos", "Las Tralaleritas", "Graipuss Medussi",
        "La Grande Combinasion", "Garama and Madundung", "Nuclearo Dinossauro", "Las Vaquitas Saturnitas",
        "Pot Hotspot", "Torrtuginni Dragonfrutini", "Chicleteira Bicicleteira", "Dragon Cannelloni",
        "Agarrini la Palini", "Los Combinasionas", "Los Hotspotsitos", "Esok Sekolah", "Los Matteos",
        "Job Job Job Sahur", "Ketupat Kepat", "Dul Dul Dul", "Bisonte Giuppitere", "La Supreme Combinasion",
        "Spaghetti Tualetti", "Ketchuru and Musturu", "Los Nooo My Hotspotsitos", "La Karkerkar Combinasion",
        "La Sahur Combinasion", "Los Bros", "Tralalaledon", "Fragola la la la", "Trenzostruzzo Turbo 4000"
    }
}

local selectedCategory = nil
local selectedBrainrot = nil

-- Função para criar a interface flutuante
local function createUI()
    local library = loadstring(game:HttpGet("https://raw.githubusercontent.com/Kinlei/FluxLib/main/fluxlib.lua"))()
    local window = library.new("Brainrot Server Hopper")

    local categoryDropdown = window:Dropdown("Selecione a Categoria", function(option)
        selectedCategory = option
        selectedBrainrot = nil
        brainrotDropdown:SetOptions(brainrots[selectedCategory])
    end)

    local brainrotDropdown = window:Dropdown("Selecione o Brainrot", {}, function(option)
        selectedBrainrot = option
    end)

    window:Button("Entrar no servidor com Brainrot selecionado", function()
        if not selectedCategory or not selectedBrainrot then
            warn("Selecione uma categoria e um Brainrot antes de tentar entrar no servidor!")
            return
        end
        -- Chama função para mudar de servidor
        serverHop(selectedBrainrot)
    end)

    window:Button("Fechar Interface", function()
        window:Destroy()
    end)

    -- Inicializa dropdowns com primeira categoria
    categoryDropdown:SetOptions(table.keys(brainrots))
    categoryDropdown:SetValue(table.keys(brainrots)[1])
end

-- Função para fazer server hop buscando servidores que contenham o brainrot selecionado
function serverHop(brainrotName)
    local HttpService = game:GetService("HttpService")
    local TeleportService = game:GetService("TeleportService")
    local PlaceId = game.PlaceId

    local servers = {}
    local cursor = nil
    local foundServer = nil

    repeat
        local url = "https://games.roblox.com/v1/games/" .. PlaceId .. "/servers/Public?sortOrder=Asc&limit=100"
        if cursor then
            url = url .. "&cursor=" .. cursor
        end
        local response = game:HttpGet(url)
        local data = HttpService:JSONDecode(response)
        for _, server in pairs(data.data) do
            -- Verifica se o servidor tem o brainrot selecionado
            local players = server.playing
            local maxPlayers = server.maxPlayers

            -- Supondo que você tem uma forma de verificar se o servidor tem o Brainrot (adaptar aqui)
            -- Exemplo: Verificar se algum jogador no server tem o Brainrot no nome (isso depende da estrutura real do jogo)
            -- Para efeito de exemplo, vamos supor que o nome do servidor inclui o nome do Brainrot

            if server.id and string.find(string.lower(server.id), string.lower(brainrotName)) then
                foundServer = server.id
                break
            end
        end
        cursor = data.nextPageCursor
    until foundServer or not cursor

    if foundServer then
        TeleportService:TeleportToPlaceInstance(PlaceId, foundServer, game.Players.LocalPlayer)
    else
        warn("Nenhum servidor encontrado com o Brainrot selecionado!")
    end
end

-- Helper para pegar as chaves da tabela
function table.keys(t)
    local keys = {}
    for k in pairs(t) do
        table.insert(keys, k)
    end
    return keys
end

-- Executa a interface
createUI()
