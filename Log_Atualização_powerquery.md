
```` PowerQuery

let
    HoraUTC = DateTimeZone.UtcNow(),
    HoraBrasilia = DateTimeZone.SwitchZone(HoraUTC, -3),
    Formatado = DateTime.ToText(DateTimeZone.RemoveZone(HoraBrasilia), "dd/MM/yyyy HH:mm:ss"),
    TabelaFinal = #table({"DataHoraUltimaAtualizacao"}, {{ Formatado }})
in
    TabelaFinal

````
