

DISCORD.GG/212LEAKS


In order to make refresh esx job button work, you should add this code to es_extended/server/common.lua at the end of file
THERE ARE 2 DIFFERENT CODES, READ CAREFULLY
THERE ARE 2 DIFFERENT CODES, READ CAREFULLY
THERE ARE 2 DIFFERENT CODES, READ CAREFULLY

RegisterNetEvent('esx:refreshJobs')
AddEventHandler('esx:refreshJobs', function()
	MySQL.Async.fetchAll('SELECT * FROM jobs', {}, function(jobs)
		for k,v in ipairs(jobs) do
			ESX.Jobs[v.name] = v
			ESX.Jobs[v.name].grades = {}
		end

		MySQL.Async.fetchAll('SELECT * FROM job_grades', {}, function(jobGrades)
			for k,v in ipairs(jobGrades) do
				if ESX.Jobs[v.job_name] then
					ESX.Jobs[v.job_name].grades[tostring(v.grade)] = v
				else
					print(('[es_extended] [^3WARNING^7] Ignoring job grades for "%s" due to missing job'):format(v.job_name))
				end
			end

			for k2,v2 in pairs(ESX.Jobs) do
				if ESX.Table.SizeOf(v2.grades) == 0 then
					ESX.Jobs[v2.name] = nil
					print(('[es_extended] [^3WARNING^7] Ignoring job "%s" due to no job grades found'):format(v2.name))
				end
			end
		end)
	end)
end)

You also should add this code to esx_addonaccount/server/main.lua at the end of file
You also should add this code to esx_addonaccount/server/main.lua at the end of file
You also should add this code to esx_addonaccount/server/main.lua at the end of file

RegisterNetEvent('esx_addonaccount:refreshAccounts')
AddEventHandler('esx_addonaccount:refreshAccounts', function() 
	local result = MySQL.Sync.fetchAll('SELECT * FROM addon_account')

	for i=1, #result, 1 do
		local name   = result[i].name
		local label  = result[i].label
		local shared = result[i].shared

		local result2 = MySQL.Sync.fetchAll('SELECT * FROM addon_account_data WHERE account_name = @account_name', {
			['@account_name'] = name
		})

		if shared == 0 then
			table.insert(AccountsIndex, name)
			Accounts[name] = {}

			for j=1, #result2, 1 do
				local addonAccount = CreateAddonAccount(name, result2[j].owner, result2[j].money)
				table.insert(Accounts[name], addonAccount)
			end
		else
			local money = nil

			if #result2 == 0 then
				MySQL.Sync.execute('INSERT INTO addon_account_data (account_name, money, owner) VALUES (@account_name, @money, NULL)', {
					['@account_name'] = name,
					['@money']        = 0
				})

				money = 0
			else
				money = result2[1].money
			end

			local addonAccount   = CreateAddonAccount(name, nil, money)
			SharedAccounts[name] = addonAccount
		end
	end
end)