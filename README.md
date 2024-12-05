Private Sub GetBays()
    ' ... existing initialization code ...

    For Each drBay In dtBay.Rows
        ' ... existing bay row creation ...

        BayCell = New TableCell
        BayCell.CssClass = "BayCell"

        ' Bay number
        If drBay.Item("BAY").ToString = "0" Then
            BayCell.Text = "Ramp"
        Else
            BayCell.Text = "Bay " & drBay.Item("BAY").ToString
        End If

        If Not IsDBNull(drBay.Item("CARRIER")) Then
            ' Carrier name in wrapped div
            BayCell.Text += "<div style='word-wrap: break-word; max-width: 300px; font-size: 40px;'><br/>" & _
                           drBay.Item("CARRIER").ToString & "</div>"
            
            ' Only add pallet details if there are pallets
            If intCompletionPercentage > 0 Then
                ' Get pallet details
                Dim dtPallets As DataTable = GetPalletDetails(drBay.Item("BAY").ToString)
                
                ' Add compact pallet view
                BayCell.Text += "<div style='font-size: 20px; margin-top: 10px; color: #333;'>"
                BayCell.Text += "<div style='display: inline-block; margin-right: 20px;'>"
                
                ' Show first few loaded pallets with ellipsis if there are more
                Dim loadedPallets As New List(Of String)
                For Each row As DataRow In dtPallets.Rows
                    loadedPallets.Add(row("PalletID").ToString())
                Next

                If loadedPallets.Count > 0 Then
                    BayCell.Text += "Loaded: " & String.Join(", ", loadedPallets.Take(3).ToArray())
                    If loadedPallets.Count > 3 Then
                        BayCell.Text += " ..."
                    End If
                End If
                
                BayCell.Text += "</div></div>"
            End If
        End If

        BayRow.Cells.Add(BayCell)

        ' ... rest of existing code for progress bar and total count ...
    Next
End Sub

' Add this method to get pallet details
Private Function GetPalletDetails(ByVal bayNumber As String) As DataTable
    Dim dt As New DataTable()
    
    Using conn As New SqlConnection(_sqlConnectionString)
        Dim cmd As New SqlCommand("SELECT PalletID, PackOrderNo, ScanTime FROM ShipmentTracking WHERE ShipmentID = @ShipmentID ORDER BY PalletID", conn)
        cmd.Parameters.AddWithValue("@ShipmentID", bayNumber)
        
        Try
            conn.Open()
            Using reader As SqlDataReader = cmd.ExecuteReader()
                dt.Load(reader)
            End Using
        Catch ex As Exception
            ' Handle error appropriately
            HandleError(ex)
        End Try
    End Using
    
    Return dt
End Function

Private Sub GetBays()
    ' ... existing initialization code ...

    For Each drBay In dtBay.Rows
        ' ... existing bay row creation ...

        BayCell = New TableCell
        BayCell.CssClass = "BayCell"

        If drBay.Item("BAY").ToString = "0" Then
            BayCell.Text = "Ramp"
        Else
            BayCell.Text = "Bay " & drBay.Item("BAY").ToString
        End If

        If Not IsDBNull(drBay.Item("CARRIER")) Then
            ' Carrier name with wrapping
            BayCell.Text += "<div style='word-wrap: break-word; max-width: 300px; font-size: 40px;'><br/>" & _
                           drBay.Item("CARRIER").ToString & "</div>"
            
            ' Only show pallet details if there are pallets
            If intCompletionPercentage > 0 Then
                ' Get pallet details using direct SQL
                Dim dtPallets As New DataTable()
                Using connSQL As New SqlConnection(ConfigurationManager.AppSettings("SQLConnectionString"))
                    Dim cmdSQL As New SqlCommand("SELECT PalletID, PackOrderNo, ScanTime " & _
                                               "FROM ShipmentTracking " & _
                                               "WHERE ShipmentID = @ShipmentID " & _
                                               "ORDER BY PalletID", connSQL)
                    
                    cmdSQL.Parameters.AddWithValue("@ShipmentID", drBay.Item("BAY").ToString)
                    
                    Try
                        connSQL.Open()
                        Using reader As SqlDataReader = cmdSQL.ExecuteReader()
                            dtPallets.Load(reader)
                        End Using

                        ' Add compact pallet view
                        BayCell.Text += "<div style='font-size: 20px; margin-top: 10px; color: #333;'>"
                        
                        ' Show loaded pallets
                        Dim loadedPallets As New ArrayList()
                        For Each row As DataRow In dtPallets.Rows
                            loadedPallets.Add(row("PalletID").ToString())
                        Next

                        If loadedPallets.Count > 0 Then
                            BayCell.Text += "Loaded: " & String.Join(", ", DirectCast(loadedPallets.ToArray(GetType(String)), String()).Take(3))
                            If loadedPallets.Count > 3 Then
                                BayCell.Text += " ..."
                            End If
                        End If
                        
                        BayCell.Text += "</div>"

                    Catch ex As Exception
                        HandleError(ex)
                    End Try
                End Using
            End If
        End If

        BayRow.Cells.Add(BayCell)

        ' ... rest of existing code for progress bar and total count ...
    Next
End Sub

Private Sub GetBays()
    ' ... existing initialization code ...

    For Each drBay In dtBay.Rows
        ' ... existing bay row creation ...

        BayCell = New TableCell
        BayCell.CssClass = "BayCell"

        If drBay.Item("BAY").ToString = "0" Then
            BayCell.Text = "Ramp"
        Else
            BayCell.Text = "Bay " & drBay.Item("BAY").ToString
        End If

        If Not IsDBNull(drBay.Item("CARRIER")) Then
            ' Carrier name with wrapping
            BayCell.Text += "<div style='word-wrap: break-word; max-width: 300px; font-size: 40px;'><br/>" & _
                           drBay.Item("CARRIER").ToString & "</div>"
            
            ' Only show pallet details if there are pallets
            If intCompletionPercentage > 0 Then
                ' Get pallet details using direct SQL
                Dim dtPallets As New DataTable()
                Using connSQL As New SqlConnection(ConfigurationManager.AppSettings("SQLConnectionString"))
                    Dim cmdSQL As New SqlCommand("SELECT PalletID, PackOrderNo, ScanTime " & _
                                               "FROM ShipmentTracking " & _
                                               "WHERE ShipmentID = @ShipmentID " & _
                                               "ORDER BY PalletID", connSQL)
                    
                    cmdSQL.Parameters.AddWithValue("@ShipmentID", drBay.Item("BAY").ToString)
                    
                    Try
                        connSQL.Open()
                        Using reader As SqlDataReader = cmdSQL.ExecuteReader()
                            dtPallets.Load(reader)
                        End Using

                        ' Get total pallets from current row
                        Dim totalPallets As Integer = CInt(drBay.Item("TOTALPALLETS"))
                        
                        ' Create list of loaded pallets
                        Dim loadedPallets As New ArrayList()
                        For Each row As DataRow In dtPallets.Rows
                            loadedPallets.Add(row("PalletID").ToString())
                        Next

                        ' Create list of remaining pallets
                        Dim remainingPallets As New ArrayList()
                        For i As Integer = 1 To totalPallets
                            Dim palletId As String = i.ToString("00")
                            If Not loadedPallets.Contains(palletId) Then
                                remainingPallets.Add(palletId)
                            End If
                        Next

                        ' Add pallets view in two columns
                        BayCell.Text += "<div style='font-size: 18px; margin-top: 10px; color: #333;'>"
                        
                        ' Show loaded pallets
                        If loadedPallets.Count > 0 Then
                            BayCell.Text += "<div style='margin-bottom: 5px;'><strong>Loaded:</strong> " & _
                                          String.Join(", ", DirectCast(loadedPallets.ToArray(GetType(String)), String())) & _
                                          "</div>"
                        End If
                        
                        ' Show remaining pallets
                        If remainingPallets.Count > 0 Then
                            BayCell.Text += "<div><strong>Remaining:</strong> " & _
                                          String.Join(", ", DirectCast(remainingPallets.ToArray(GetType(String)), String())) & _
                                          "</div>"
                        End If
                        
                        BayCell.Text += "</div>"

                    Catch ex As Exception
                        HandleError(ex)
                    End Try
                End Using
            End If
        End If

        BayRow.Cells.Add(BayCell)

        ' ... rest of existing code for progress bar and total count ...
    Next
End Sub
