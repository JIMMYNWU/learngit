        Select Case type
            Case "Consignment Note"
                _xmlxml = XmlReader.Create((New StringReader(ViewState("CONNOTE" + pcklstno).ToString)))
                _xmlxsl = XmlReader.Create(MapPath("./XslFile/HTMLConsignmentNoteRenderer.xsl"))
                FILENAME = MapPath("~\Temp\" & BATCHID & pcklstno & "_CONNOTE.PDF")
                FILENAME2 = "~\Temp\" & BATCHID & pcklstno & "_CONNOTE.PDF"
            Case "Address Label"
                _xmlxml = XmlReader.Create((New StringReader(ViewState("LABEL" + pcklstno).ToString)))
                _xmlxsl = XmlReader.Create(MapPath("./XslFile/HTMLAddressLabelRenderer.xsl"))
                FILENAME = MapPath("~\Temp\" & BATCHID & pcklstno & "_LABEL.PDF")
                FILENAME2 = "~\Temp\" & BATCHID & pcklstno & "_LABEL.PDF"
            Case "Detailed Manifest"
                _xmlxml = XmlReader.Create((New StringReader(ViewState("MANIFEST" + pcklstno).ToString)))
                _xmlxsl = XmlReader.Create(MapPath("./XslFile/HTMLManifestRenderer.xsl"))
                FILENAME = MapPath("~\Temp\" & BATCHID & pcklstno & "_MANIFEST.PDF")
                FILENAME2 = "~\Temp\" & BATCHID & pcklstno & "_MANIFEST.PDF"
            Case "Invoice"
                _xmlxml = XmlReader.Create((New StringReader(ViewState("INVOICE" + pcklstno).ToString)))
                _xmlxsl = XmlReader.Create(MapPath("./XslFile/HTMLCommercialInvoiceRenderer.xsl"))
                FILENAME = MapPath("~\Temp\" & BATCHID & pcklstno & "_INVOICE.PDF")
                FILENAME2 = "~\Temp\" & BATCHID & pcklstno & "_INVOICE.PDF"
            Case "Return Consignment Note"
                _xmlxml = XmlReader.Create((New StringReader(ViewState("CONNOTE_R" + pcklstno).ToString)))
                _xmlxsl = XmlReader.Create(MapPath("./XslFile/HTMLConsignmentNoteRenderer.xsl"))
                FILENAME = MapPath("~\Temp\" & BATCHID & pcklstno & "_CONNOTE_R.PDF")
                FILENAME2 = "~\Temp\" & BATCHID & pcklstno & "_CONNOTE_R.PDF"
            Case "Return Address Label"
                _xmlxml = XmlReader.Create((New StringReader(ViewState("LABEL_R" + pcklstno).ToString)))
                _xmlxsl = XmlReader.Create(MapPath("./XslFile/HTMLAddressLabelRenderer.xsl"))
                FILENAME = MapPath("~\Temp\" & BATCHID & pcklstno & "_LABEL_R.PDF")
                FILENAME2 = "~\Temp\" & BATCHID & pcklstno & "_LABEL_R.PDF"
            Case "Return Detailed Manifest"
                _xmlxml = XmlReader.Create((New StringReader(ViewState("MANIFEST_R" + pcklstno).ToString)))
                _xmlxsl = XmlReader.Create(MapPath("./XslFile/HTMLManifestRenderer.xsl"))
                FILENAME = MapPath("~\Temp\" & BATCHID & pcklstno & "_MANIFEST_R.PDF")
                FILENAME2 = "~\Temp\" & BATCHID & pcklstno & "_MANIFEST_R.PDF"
            Case "Return Invoice"
                _xmlxml = XmlReader.Create((New StringReader(ViewState("INVOICE_R" + pcklstno).ToString)))
                _xmlxsl = XmlReader.Create(MapPath("./XslFile/HTMLCommercialInvoiceRenderer.xsl"))
                FILENAME = MapPath("~\Temp\" & BATCHID & pcklstno & "_INVOICE_R.PDF")
                FILENAME2 = "~\Temp\" & BATCHID & pcklstno & "_INVOICE_R.PDF"
        End Select







	   Dim xslct As XslCompiledTransform = New XslCompiledTransform
        Dim myXsltSettings As New XsltSettings()
        myXsltSettings.EnableDocumentFunction = True
        xslct.Load(_xmlxsl, myXsltSettings, Nothing)
        Dim ms As MemoryStream = New MemoryStream
        Dim xmltxtWr As XmlTextWriter = New XmlTextWriter(ms, Nothing)
        xslct.Transform(_xmlxml, xmltxtWr)
        Dim st As StreamReader = New StreamReader(ms)
        ms.Seek(0, SeekOrigin.Begin)
        Dim TransformedHTML As String = st.ReadToEnd
        TransformedHTML = TransformedHTML.Replace("<script>includePageBreak();</script>", "</table><div STYLE='page-break-after: always;'></div>")
        TransformedHTML = TransformedHTML.Replace("letter-spacing: 0.05cm", "")
        TransformedHTML = TransformedHTML.Replace("letter-spacing: 0.2cm;", "")
        TransformedHTML = TransformedHTML.Replace("letter-spacing: 0.1cm;", "")
        TransformedHTML = TransformedHTML.Replace(" ", "")
        TransformedHTML = TransformedHTML.Replace(".hr", ".hr2")
        TransformedHTML = TransformedHTML.Replace("RMA", param2("RMANO"))
        TransformedHTML = TransformedHTML.Replace("IO#", param2("IO"))
        Dim document As IPdfDocument = Pdf.From(TransformedHTML)
        If (IntPtr.Size = 4) Then
            document = document.WithObjectSetting("load.zoomFactor", "1.5")
        End If
        Dim fs As FileStream = New FileStream(FILENAME, FileMode.Create)
        Dim bw As BinaryWriter = New BinaryWriter(fs)
        bw.Write(document.Content)
        bw.Close()
        fs.Close()
        Return FILENAME
