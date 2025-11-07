local term = require("term")

local options = {"Salvar posiÃ§Ã£o", "Teleportar para posiÃ§Ã£o salva", "Sair"}
local selected = 1

local function drawMenu()
    io.write("\27[2J\27[H") -- Limpa a tela
    io.write("Use â† â†’ para navegar e Enter para selecionar\n\n")
    for i, opt in ipairs(options) do
        if i == selected then
            io.write(string.format(" [%s] ", opt))
        else
            io.write(string.format("  %s  ", opt))
        end
    end
    io.write("\n\n")
end

-- FunÃ§Ãµes do teleporte (igual exemplo anterior)
local saveFile = "posicao.dat"

local function getCurrentPosition()
    print("Digite sua posiÃ§Ã£o atual (x y z):")
    local x, y, z = io.read("*n", "*n", "*n")
    return {x = x, y = y, z = z}
end

local function savePosition(pos)
    local file = io.open(saveFile, "w")
    if file then
        file:write(string.format("%f %f %f\n", pos.x, pos.y, pos.z))
        file:close()
        print("PosiÃ§Ã£o salva com sucesso!")
    else
        print("Erro ao salvar posiÃ§Ã£o!")
    end
end

local function loadPosition()
    local file = io.open(saveFile, "r")
    if file then
        local x, y, z = file:read("*n", "*n", "*n")
        file:close()
        if x and y and z then
            return {x = x, y = y, z = z}
        end
    end
    return nil
end

local function teleportToPosition(pos)
    print(string.format("Teleportando para posiÃ§Ã£o: x=%.2f, y=%.2f, z=%.2f", pos.x, pos.y, pos.z))
end

-- Loop de menu
while true do
    drawMenu()
    local key = term.key()

    if key == term.KEY_LEFT then
        selected = selected - 1
        if selected < 1 then selected = #options end
    elseif key == term.KEY_RIGHT then
        selected = selected + 1
        if selected > #options then selected = 1 end
    elseif key == term.KEY_ENTER then
        if selected == 1 then
            local pos = getCurrentPosition()
            savePosition(pos)
        elseif selected == 2 then
            local pos = loadPosition()
            if pos then
                teleportToPosition(pos)
            else
                print("Nenhuma posiÃ§Ã£o salva encontrada!")
            end
        elseif selected == 3 then
            print("Saindo...")
            break
        end
        print("Pressione qualquer tecla para voltar ao menu...")
        term.key()
    end
end
