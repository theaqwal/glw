<%@ Page Language="VB" MasterPageFile="~/FullScreen.master" AutoEventWireup="true" CodeFile="PalletDetails.aspx.vb" Inherits="PalletDetails" Title="Pallet Details" %>

<asp:Content ID="Content1" ContentPlaceHolderID="cphBody" runat="Server">
    <style type="text/css">
        /* Embedded styles will have higher priority */
        body {
            font-family: verdana;
            font-weight: bold;
            font-size: xx-small;
            color: black;
            background-color: #ffffff;
        }

        .shipment-title {
            font-size: 55px;
            color: #00008B;
            font-weight: bold;
            margin-bottom: 20px;
        }

        .section-title {
            font-size: 40px;
            color: #00008B;
            margin-top: 20px;
        }

        .pallet-grid {
            width: 100%;
            border: 2px solid #00008B;
            margin: 10px 0;
        }

        .pallet-grid th {
            background-color: #00008B;
            color: white;
            padding: 10px;
            font-size: 24px;
            text-align: left;
        }

        .pallet-grid td {
            padding: 8px;
            font-size: 24px;
            border-bottom: 1px solid #ddd;
        }

        .pallet-grid tr:nth-child(even) {
            background-color: #f5f5f5;
        }

        .back-link {
            display: inline-block;
            background-color: #009225;
            color: white;
            padding: 15px 30px;
            font-size: 24px;
            text-decoration: none;
            border-radius: 8px;
            margin-top: 20px;
        }

        .back-link:hover {
            background-color: #00A828;
        }

        .summary-text {
            font-size: 30px;
            color: #00008B;
            margin: 20px 0;
            display: block;
        }
    </style>

    <div style="padding: 20px;">
        <div class="shipment-title">
            Shipment Details - Bay <asp:Label ID="lblBayNumber" runat="server" />
        </div>
        
        <div class="section-title">Loaded Pallets</div>
        <asp:GridView ID="gvLoadedPallets" runat="server" CssClass="pallet-grid" 
                     AutoGenerateColumns="False" GridLines="None">
            <Columns>
                <asp:BoundField DataField="PalletID" HeaderText="Pallet #" />
                <asp:BoundField DataField="PackOrderNo" HeaderText="Pack Order" />
                <asp:BoundField DataField="ScanTime" HeaderText="Scan Time" 
                              DataFormatString="{0:MM/dd/yyyy HH:mm:ss}" />
            </Columns>
        </asp:GridView>

        <div class="section-title">Remaining Pallets</div>
        <asp:GridView ID="gvRemainingPallets" runat="server" CssClass="pallet-grid" 
                     AutoGenerateColumns="False" GridLines="None">
            <Columns>
                <asp:BoundField DataField="PalletID" HeaderText="Pallet #" />
            </Columns>
        </asp:GridView>

        <asp:Label ID="lblSummary" runat="server" CssClass="summary-text" />

        <div>
            <asp:HyperLink ID="hlBack" runat="server" NavigateUrl="Default.aspx" 
                          CssClass="back-link">← Back to Shipping Dock</asp:HyperLink>
        </div>
    </div>
</asp:Content>
