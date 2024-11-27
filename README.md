<%@ Page Language="VB" MasterPageFile="~/FullScreen.master" AutoEventWireup="true" CodeFile="PalletDetails.aspx.vb" Inherits="PalletDetails" Title="Pallet Details" %>

<asp:Content ID="Content1" ContentPlaceHolderID="cphBody" runat="Server">
    <div class="pallet-details-container">
        <h1 class="bay-header">Shipment Details - Bay <asp:Label ID="lblBayNumber" runat="server" /></h1>
        
        <div class="pallet-section">
            <h2 class="section-header">Loaded Pallets</h2>
            <asp:GridView ID="gvLoadedPallets" runat="server" CssClass="pallet-grid" 
                         AutoGenerateColumns="False" GridLines="None">
                <Columns>
                    <asp:BoundField DataField="PalletID" HeaderText="Pallet #" 
                        ItemStyle-CssClass="pallet-number" HeaderStyle-CssClass="grid-header" />
                    <asp:BoundField DataField="PackOrderNo" HeaderText="Pack Order" 
                        ItemStyle-CssClass="pack-order" HeaderStyle-CssClass="grid-header" />
                    <asp:BoundField DataField="ScanTime" HeaderText="Scan Time" 
                        DataFormatString="{0:MM/dd/yyyy HH:mm:ss}"
                        ItemStyle-CssClass="scan-time" HeaderStyle-CssClass="grid-header" />
                </Columns>
            </asp:GridView>
        </div>

        <div class="pallet-section">
            <h2 class="section-header">Remaining Pallets</h2>
            <asp:GridView ID="gvRemainingPallets" runat="server" CssClass="pallet-grid" 
                         AutoGenerateColumns="False" GridLines="None">
                <Columns>
                    <asp:BoundField DataField="PalletID" HeaderText="Pallet #" 
                        ItemStyle-CssClass="pallet-number" HeaderStyle-CssClass="grid-header" />
                </Columns>
            </asp:GridView>
        </div>

        <div class="summary-section">
            <asp:Label ID="lblSummary" runat="server" CssClass="progress-text" />
        </div>

        <div class="navigation-section">
            <asp:HyperLink ID="hlBack" runat="server" NavigateUrl="Default.aspx" 
                          CssClass="back-button">&#x2190; Back to Shipping Dock</asp:HyperLink>
        </div>
    </div>
</asp:Content>
