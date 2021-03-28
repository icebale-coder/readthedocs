# FC 

## FC диагностика

### Просмотр HBA устройств

<details><summary>lspci -nn | grep -i hba</summary>
<p>

```bash
[root@server1]# lspci -nn | grep -i hba
06:00.0 Fibre Channel [0c04]: QLogic Corp. ISP2432-based 4Gb Fibre Channel to PCI Express HBA [1077:2432] (rev 03)
18:00.0 Fibre Channel [0c04]: QLogic Corp. ISP2432-based 4Gb Fibre Channel to PCI Express HBA [1077:2432] (rev 03)

```
</details>
</p>

[root@server1]# systool -c fc_host -v

[root@server1]# ls -l /dev/disk/by-path/ | grep -v part


#### Модификация/Редактирования данных в репозитарии:
```bash
# 1. Модифим/Редактируем данные репозитария

# 2. Подхватываем текщие изменения для комитта
# to include in what will be committed
git add .

# 3. Фиксируем текущие изменения в отдельном комитте
# commit 
git commit -m "My diff"

# 4. Отправляем изменения на github
# to push to github repo
git push
```

