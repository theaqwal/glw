'PalletDetails.aspx
<%@ Page Language="VB" AutoEventWireup="true" CodeFile="PalletDetails.aspx.vb" Inherits="PalletDetails" %>
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
    <title>Pallet Details</title>
    <link href="Styles.css" rel="stylesheet" type="text/css" />
    <meta http-equiv="refresh" content="30">
</head>
<body>
    <form id="form1" runat="server">
        <div class="pallet-details-container">
            <h1>Shipment Details - Bay <asp:Label ID="lblBayNumber" runat="server" /></h1>
            
            <div class="pallet-section">
                <h2>Loaded Pallets</h2>
                <asp:GridView ID="gvLoadedPallets" runat="server" CssClass="pallet-grid" AutoGenerateColumns="False">
                    <Columns>
                        <asp:BoundField DataField="PalletID" HeaderText="Pallet #" />
                        <asp:BoundField DataField="PackOrderNo" HeaderText="Pack Order" />
                        <asp:BoundField DataField="ScanTime" HeaderText="Scan Time" DataFormatString="{0:MM/dd/yyyy HH:mm:ss}" />
                    </Columns>
                </asp:GridView>
            </div>

            <div class="pallet-section">
                <h2>Remaining Pallets</h2>
                <asp:GridView ID="gvRemainingPallets" runat="server" CssClass="pallet-grid" AutoGenerateColumns="False">
                    <Columns>
                        <asp:BoundField DataField="PalletID" HeaderText="Pallet #" />
                    </Columns>
                </asp:GridView>
            </div>

            <div class="summary-section">
                <asp:Label ID="lblSummary" runat="server" CssClass="summary-text" />
            </div>

            <div class="navigation-section">
                <asp:HyperLink ID="hlBack" runat="server" NavigateUrl="Default.aspx" CssClass="BackButton">Back to Shipping Dock</asp:HyperLink>
            </div>
        </div>
    </form>
</body>
</html>

'PalletDetails.aspx.vb
Partial Class PalletDetails
    Inherits System.Web.UI.Page

    Private ReadOnly _palletTrackingDAL As New PalletTrackingDAL()

    Protected Sub Page_Load(ByVal sender As Object, ByVal e As EventArgs) Handles Me.Load
        If Not IsPostBack Then
            Try
                LoadPalletData()
            Catch ex As Exception
                ' Log the error
                Response.Write("An error occurred while loading pallet data. Please try refreshing the page.")
            End Try
        End If
    End Sub

    Private Sub LoadPalletData()
        Dim shipmentId As String = Request.QueryString("ShipmentID")
        
        If String.IsNullOrEmpty(shipmentId) Then
            Throw New ArgumentException("Shipment ID is required")
        End If

        lblBayNumber.Text = shipmentId

        ' Get total pallets for this shipment
        Dim totalPallets As Integer = _palletTrackingDAL.GetTotalPalletsForShipment(shipmentId)
        
        ' Get scanned pallets
        Dim dtScanned As DataTable = _palletTrackingDAL.GetScannedPallets(shipmentId)
        
        ' Bind scanned pallets
        gvLoadedPallets.DataSource = dtScanned
        gvLoadedPallets.DataBind()

        ' Create list of remaining pallets
        Dim dtRemaining As New DataTable()
        dtRemaining.Columns.Add("PalletID", GetType(String))

        ' Get list of scanned pallet IDs
        Dim scannedPallets As New HashSet(Of String)
        For Each row As DataRow In dtScanned.Rows
            scannedPallets.Add(row("PalletID").ToString())
        Next

        ' Add unscanned pallet numbers to remaining pallets
        For i As Integer = 1 To totalPallets
            Dim palletId As String = i.ToString("00")
            If Not scannedPallets.Contains(palletId) Then
                dtRemaining.Rows.Add(palletId)
            End If
        Next

        ' Bind remaining pallets
        gvRemainingPallets.DataSource = dtRemaining
        gvRemainingPallets.DataBind()

        ' Update summary
        lblSummary.Text = String.Format("Progress: {0} of {1} pallets loaded", _
                                      dtScanned.Rows.Count, _
                                      totalPallets)
    End Sub
End Class
