
# [ESX]
For those using ESX it needs to use it with some operations

save sql data to database

enter file
es_extended>server>main.lua

remove
```lua
RegisterNetEvent('esx:onPlayerJoined')
AddEventHandler('esx:onPlayerJoined', function()
	if not ESX.Players[source] then
		onPlayerJoined(source)
	end
end)
```


# Add the following codes according to the sql structure you use.

## [mysql-async](https://github.com/brouznouf/fivem-mysql-async)

```lua
local StringCharset = {}
local NumberCharset = {}

for i = 48,  57 do table.insert(NumberCharset, string.char(i)) end
for i = 65,  90 do table.insert(StringCharset, string.char(i)) end
for i = 97, 122 do table.insert(StringCharset, string.char(i)) end

function RandomStr(length)
	if length > 0 then
		return RandomStr(length-1) .. StringCharset[math.random(1, #StringCharset)]
	else
		return ''
	end
end

function RandomInt(length)
	if length > 0 then
		return RandomInt(length-1) .. NumberCharset[math.random(1, #NumberCharset)]
	else
		return ''
	end
end

function SplitStr(str, delimiter)
	local result = { }
	local from  = 1
	local delim_from, delim_to = string.find( str, delimiter, from  )
	while delim_from do
		table.insert( result, string.sub( str, from , delim_from-1 ) )
		from  = delim_to + 1
		delim_from, delim_to = string.find( str, delimiter, from  )
	end
	table.insert( result, string.sub( str, from  ) )
	return result
end

ESX.Login = function(source, citizenid, newData) 
	if source ~= nil then
		if citizenid ~= false then
			loadESXPlayer(citizenid,source)
		else
			local accounts = {}
			for account,money in pairs(Config.StartingAccountMoney) do
				accounts[account] = money
			end
			local playerData = {}
			local identifier
			local charinfo = {}
			firstname = newData.charinfo.firstname
			lastname = newData.charinfo.lastname
			sex = newData.charinfo.sex
			birthdate = newData.charinfo.date
			cid = newData.cid
			-- local citizenId = GetPlayerIdentifiers(source)[1]
			local citizenId = CreateCharId(source)
			MySQL.Async.execute('INSERT INTO users (accounts, identifier, lastname, firstname, sex, dateofbirth) VALUES (@accounts, @identifier, @lastname, @firstname, @sex, @dateofbirth)', {
				['@accounts'] = json.encode(accounts),
				['@identifier'] = citizenId,
				['@firstname']  = firstname,
				['@lastname']  = lastname,
				['@sex']  = sex,
				['@dateofbirth']  = birthdate
			}, function(rowsChanged)
				loadESXPlayer(citizenId,source)
			end)
		end
		return true
	else
		return false
	end
end



function CreateCharId(source)
	local UniqueFound = false
	local CitizenId = nil

	while not UniqueFound do
		CitizenId = tostring(RandomStr(3) .. RandomInt(5)):upper() .. ":"..GetPlayerIdentifiers(source)[1]
		MySQL.Sync.fetchAll("SELECT * FROM users WHERE  identifier LIKE '%"..CitizenId.."%'", {}, function(result)
			if #result == 0 then
				UniqueFound = true
			end
		end)
		
	
	end
	
	local CitizenId = CitizenId
	return CitizenId
end
```

## [ghmattimysql](https://github.com/FrazzIe/ghmattimysql)
```lua
local StringCharset = {}
local NumberCharset = {}

for i = 48,  57 do table.insert(NumberCharset, string.char(i)) end
for i = 65,  90 do table.insert(StringCharset, string.char(i)) end
for i = 97, 122 do table.insert(StringCharset, string.char(i)) end

function RandomStr(length)
	if length > 0 then
		return RandomStr(length-1) .. StringCharset[math.random(1, #StringCharset)]
	else
		return ''
	end
end

function RandomInt(length)
	if length > 0 then
		return RandomInt(length-1) .. NumberCharset[math.random(1, #NumberCharset)]
	else
		return ''
	end
end

function SplitStr(str, delimiter)
	local result = { }
	local from  = 1
	local delim_from, delim_to = string.find( str, delimiter, from  )
	while delim_from do
		table.insert( result, string.sub( str, from , delim_from-1 ) )
		from  = delim_to + 1
		delim_from, delim_to = string.find( str, delimiter, from  )
	end
	table.insert( result, string.sub( str, from  ) )
	return result
end

ESX.Login = function(source, citizenid, newData) 
	if source ~= nil then
		if citizenid ~= false then
			loadESXPlayer(citizenid,source)
		else
			local accounts = {}
			for account,money in pairs(Config.StartingAccountMoney) do
				accounts[account] = money
			end
			local playerData = {}
			local identifier
			local charinfo = {}
			firstname = newData.charinfo.firstname
			lastname = newData.charinfo.lastname
			sex = newData.charinfo.sex
			birthdate = newData.charinfo.date
			cid = newData.cid
			-- local citizenId = GetPlayerIdentifiers(source)[1]
			local citizenId = CreateCharId(source)
			exports.ghmattimysql:execute('INSERT INTO users (accounts, identifier, lastname, firstname, sex, dateofbirth) VALUES (@accounts, @identifier, @lastname, @firstname, @sex, @dateofbirth)', {
				['@accounts'] = json.encode(accounts),
				['@identifier'] = citizenId,
				['@firstname']  = firstname,
				['@lastname']  = lastname,
				['@sex']  = sex,
				['@dateofbirth']  = birthdate
			}, function(rowsChanged)
				loadESXPlayer(citizenId,source)
			end)
		end
		return true
	else
		return false
	end
end



function CreateCharId(source)
	local UniqueFound = false
	local CitizenId = nil

	while not UniqueFound do
		CitizenId = tostring(RandomStr(3) .. RandomInt(5)):upper() .. ":"..GetPlayerIdentifiers(source)[1]
		exports.ghmattimysql:executeSync("SELECT * FROM users WHERE  identifier LIKE '%"..CitizenId.."%'", {}, function(result)
			if #result == 0 then
				UniqueFound = true
			end
		end)
		
	
	end
	
	local CitizenId = CitizenId
	return CitizenId
end
```

## [oxmysql](https://github.com/overextended/oxmysql)
```lua

local StringCharset = {}
local NumberCharset = {}

for i = 48,  57 do table.insert(NumberCharset, string.char(i)) end
for i = 65,  90 do table.insert(StringCharset, string.char(i)) end
for i = 97, 122 do table.insert(StringCharset, string.char(i)) end

function RandomStr(length)
	if length > 0 then
		return RandomStr(length-1) .. StringCharset[math.random(1, #StringCharset)]
	else
		return ''
	end
end

function RandomInt(length)
	if length > 0 then
		return RandomInt(length-1) .. NumberCharset[math.random(1, #NumberCharset)]
	else
		return ''
	end
end

function SplitStr(str, delimiter)
	local result = { }
	local from  = 1
	local delim_from, delim_to = string.find( str, delimiter, from  )
	while delim_from do
		table.insert( result, string.sub( str, from , delim_from-1 ) )
		from  = delim_to + 1
		delim_from, delim_to = string.find( str, delimiter, from  )
	end
	table.insert( result, string.sub( str, from  ) )
	return result
end

ESX.Login = function(source, citizenid, newData) 
	if source ~= nil then
		if citizenid ~= false then
			loadESXPlayer(citizenid,source)
		else
			local accounts = {}
			for account,money in pairs(Config.StartingAccountMoney) do
				accounts[account] = money
			end
			local playerData = {}
			local identifier
			local charinfo = {}
			firstname = newData.charinfo.firstname
			lastname = newData.charinfo.lastname
			sex = newData.charinfo.sex
			birthdate = newData.charinfo.date
			cid = newData.cid
			-- local citizenId = GetPlayerIdentifiers(source)[1]
			local citizenId = CreateCharId(source)
			exports.oxmysql:execute('INSERT INTO users (accounts, identifier, lastname, firstname, sex, dateofbirth) VALUES (@accounts, @identifier, @lastname, @firstname, @sex, @dateofbirth)', {
				['@accounts'] = json.encode(accounts),
				['@identifier'] = citizenId,
				['@firstname']  = firstname,
				['@lastname']  = lastname,
				['@sex']  = sex,
				['@dateofbirth']  = birthdate
			}, function(rowsChanged)
				loadESXPlayer(citizenId,source)
			end)
		end
		return true
	else
		return false
	end
end



function CreateCharId(source)
	local UniqueFound = false
	local CitizenId = nil

	while not UniqueFound do
		CitizenId = tostring(RandomStr(3) .. RandomInt(5)):upper() .. ":"..GetPlayerIdentifiers(source)[1]
		local result = exports.oxmysql:executeSync("SELECT * FROM users WHERE  identifier LIKE '%"..CitizenId.."%'", {})
		if #result == 0 then
			UniqueFound = true
		end
	
	end
	
	local CitizenId = CitizenId
	return CitizenId
end
```








