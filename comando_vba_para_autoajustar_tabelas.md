# Comandos uteis de VBA

Comando para auto ajustar as colunas ao texto dos produtos. 



````vba
Private Sub Worksheet_SelectionChange(ByVal Target As Range)
Cells.EntireColumn.AutoFit
Cells.EntireRow.AutoFit
End Sub
````
