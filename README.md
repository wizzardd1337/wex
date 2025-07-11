-- В этом месте ты в executor вставляешь ключ в переменную
local userKey = "ВСТАВЬ_СЮДА_КЛЮЧ"

-- Таблица ключей с временем действия в минутах
local keys = {
    ["KEY123"] = 60,    -- 60 минут
    ["KEY456"] = 1440,  -- 1 день
}

local HttpService = game:GetService("HttpService")
local player = game:GetService("Players").LocalPlayer

-- Ключ для сохранения данных локально (в этом примере через PlayerPrefs с помощью Set/GetAsync нет, но можно использовать DataStore на сервере)
-- Для простоты — сохраним в локальном хранилище с помощью Roblox DataStore на сервере, а здесь эмуляция:
-- Чтобы без сервера — храним в Value в самом скрипте (потеряется при перезаходе)

-- Здесь простой локальный кэш времени активации
local activationTimeValue = Instance.new("IntValue")
activationTimeValue.Name = "ActivationTime"
activationTimeValue.Parent = player

local function isKeyValid(key)
    local validMinutes = keys[key]
    if not validMinutes then
        return false, "Ключ не найден"
    end

    local currentTime = os.time()

    -- Сохраняем время первой активации в Value
    if activationTimeValue.Value == 0 then
        activationTimeValue.Value = currentTime
    end

    local elapsed = (currentTime - activationTimeValue.Value) / 60

    if elapsed > validMinutes then
        return false, "Срок действия ключа истёк"
    end

    return true, validMinutes - math.floor(elapsed)
end

local valid, info = isKeyValid(userKey)

if valid then
    print("Ключ валиден, осталось минут: "..info)
    -- Запускаем твой скрипт
    loadstring(game:HttpGet("https://raw.githubusercontent.com/wizzardd1337/adminguii/main/admingui", true))()
else
    warn("Ошибка активации: "..info)
    -- Тут можно заблокировать дальнейшее выполнение скрипта
end
