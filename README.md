
Private Sub GetBays()
    Try
        ' Your existing variable declarations...

        For Each drBay In dtBay.Rows
            ' Start the Bay row
            BayRow = New TableRow

            ' Calculate completion percentage
            If IsDBNull(drBay.Item("CARRIER")) Or IsDBNull(drBay.Item("TOTALPALLETS")) Or IsDBNull(drBay.Item("TOTALPALLETSLOADED")) Then
                intCompletionPercentage = 0
            ElseIf CInt(drBay.Item("TOTALPALLETS")) = 0 Then
                intCompletionPercentage = 0
            Else
                intCompletionPercentage = CInt((CInt(drBay.Item("TOTALPALLETSLOADED")) / CInt(drBay.Item("TOTALPALLETS"))) * 100)
            End If

            ' Bay and Carrier Cell
            BayCell = New TableCell
            BayCell.CssClass = "BayCell"

            If drBay.Item("BAY").ToString = "0" Then
                BayCell.Text = "Ramp"
            Else
                BayCell.Text = "Bay " & drBay.Item("BAY").ToString
            End If

            If Not IsDBNull(drBay.Item("CARRIER")) Then
                ' Add carrier name with line break
                BayCell.Text += "<br/>" & drBay.Item("CARRIER").ToString
                
                ' Add view details button if active
                If intCompletionPercentage > 0 Then
                    BayCell.Text += "<br/><div class='view-details-container'>"
                    BayCell.Text += "<a href='PalletDetails.aspx?ShipmentID=" & drBay.Item("BAY").ToString & "' "
                    BayCell.Text += "class='view-details-button'>VIEW PALLET DETAILS &#x27A4;</a></div>"
                End If
            End If
            BayRow.Cells.Add(BayCell)

            ' Progress Bar Cell
            BayCell = New TableCell
            tblProgressBar = New Table
            ProgressBarRow = New TableRow

            ' Progress Bar Completed Cell
            ProgressBarCell = New TableCell
            ProgressBarCell.Width = (intCompletionPercentage * CInt(ConfigurationManager.AppSettings("ProgressBarWidthMultiplier"))).ToString

            If Not IsDBNull(drBay.Item("CARRIER")) Then
                ProgressBarCell.CssClass = "HighlightGreen"
            Else
                ProgressBarCell.CssClass = "HighlightWhite"
            End If

            If intCompletionPercentage >= 15 Then
                ProgressBarCell.Text = intCompletionPercentage & "%"
            End If
            ProgressBarRow.Cells.Add(ProgressBarCell)

            ' Progress Bar NOT Completed Cell
            ProgressBarCell = New TableCell
            ProgressBarCell.Width = ((100 - intCompletionPercentage) * CInt(ConfigurationManager.AppSettings("ProgressBarWidthMultiplier"))).ToString

            If Not IsDBNull(drBay.Item("CARRIER")) Then
                ProgressBarCell.CssClass = "HighlightRed"
            Else
                ProgressBarCell.CssClass = "HighlightWhite"
                ProgressBarCell.Text = "Not in use"
            End If
            ProgressBarRow.Cells.Add(ProgressBarCell)

            tblProgressBar.Rows.Add(ProgressBarRow)
            BayCell.Controls.Add(tblProgressBar)
            BayRow.Cells.Add(BayCell)

            ' Pallet Information Cell
            BayCell = New TableCell
            BayCell.CssClass = "PalletCell"
            If Not IsDBNull(drBay.Item("CARRIER")) Then
                BayCell.Text = "Loaded:<br/>"
                BayCell.Text += drBay.Item("TOTALPALLETSLOADED").ToString
                BayCell.Text += " of "
                BayCell.Text += drBay.Item("TOTALPALLETS").ToString
            End If
            BayRow.Cells.Add(BayCell)

            ' Add the completed Bay row
            tblBay.Rows.Add(BayRow)
        Next

    Catch ex As Exception
        HandleError(ex)
    Finally
        cnShipmentTracking.Close()
        dtShipmentTracking.Dispose()
        dtBay.Dispose()
    End Try
End Sub
