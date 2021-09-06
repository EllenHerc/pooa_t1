## Trabalho 1 - Programação Orientada a Obejetos avançada
<p>Docente: Delano Medeiros Beder<br>
   Discente: Ellen Cristina Herculano - 743528</p>

# Princípio da Responsabilidade Única 

<p>Formulado por Robert Martin, o Princípio da Responsabilidade Unica (PRU) se consolidou como um dos princípios fundamentais da programação orientada a obejetos. O que este principio define é que cada classe/módulo de um programa deve ser responsável por apenas uma funcionalidade, ou melhor, que cada classe/módulo de um programa deve ter apenas um motivo para mudança, tendo em vista que Martin define a responsabilidade como <i>'motivo para mudança'</i>.</p>
<p>Sendo assim, a máxima do PRU é descrita da seguinte forma: <b>"uma classe só pode ter um e somente um motivo para ser modificada"</b></p>

 
<p>Ao observar um código que atende o princípio, nota-se que as depências entre classes é mínima e que há o encapsulamento das implementações que competem uma mesma resposabilidade (motivo para mudança).
<br>Desta forma, o principal motivo para se adotar o PRU é que, se o princípio for bem aplicado, gera um código robusto e coeso, o que diminui as chances de erros relacionados principalmente a futuras mudanças nas implementações das classes/módulos.</p>

### Exemplo

<p>O seguinte código busca ilustrar como pode-se aplicar o princípio para obter o melhor encapsulamento das implementações, ele define uma classe para manipulação de documentos PDF's com a auxílio da biblioteca Apache PDFBox</p>

~~~Java
public class PdfManage {

    // Método responsável por imprimir PDF na tela
    public void printPDF(PDDocument document, int page) {
        System.out.println("Inicio");
        PDFTextStripper pdfStripper = null;
        COSDocument cosDoc = null;
        try {
            PDFParser parser = new PDFParser(new FileInputStream(file));
            parser.parse();
            cosDoc = parser.getDocument();
            pdfStripper = new PDFTextStripper();
            document = new PDDocument(cosDoc);
            //Começa a leitura do arquivo a partir da página informada
            pdfStripper.setStartPage(page);

            pdfStripper.setEndPage(pdfStripper.getEndPage());
            String parsedText = pdfStripper.getText(document);

            Scanner s = new Scanner(parsedText);
            s.useDelimiter("\n");

            String linha = "";
            while (s.hasNext()) {
                linha = s.next();
                System.out.println(linha);
            }
            s.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        System.out.println("Fim");

    }
    
    // Método responsável por adicionar uma nova string no pdf em nova página
    public PDFDocument addOnPDF(PDDocument document, String string) {
        PDPage page = new PDPage();
        document.addPage(page);
        PDFont font = PDType1Font.COURIER_BOLD;
        PDPageContentStream contentStream = new PDPageContentStream(document, page);
        contentStream.beginText();
        contentStream.setFont(font, 20);
        contentStream.moveTextPositionByAmount(100, 200);
        contentStream.drawString(string); //adiciona a string ao pdf
        contentStream.endText();
        contentStream.close();

        document.save();
        System.out.print("O arquivo foi gerado com sucesso.");
        document.close();
    }

    // Método responsável por substituir uma string no PDF
    public PDDocument replaceOnPdf(PDDocument document, String searchString, String replacement) throws IOException {
        //implementação da pesquisa e substituição de strings no documento pdf
        document.save();
        System.out.print("A string foi substituida");
        document.close();
        return document;
    }
    
    // Método responsável por remover uma string no PDF
    public PDDocument removeOnPdf(PDDocument document, String string) throws IOException {
        //implementação da localização e remoção da string no documento pdf
        document.save();
        System.out.print("A string foi substituida");
        document.close();
        return document;
    }
}
~~~
<p>Embora a implementação pareça adequada a seu propósito, nota-se que a classe <i>PdfManege</i> contém duas resposabilidades: imprimir o conteúdo de um arquivo PDF e manipular strings num arquivo PDF, portanto não atende ao PRU. Para adequa-la ao princípio pode-se dividir a implementação dos métodos em classes distintas, de forma que cada classe tenha apenas uma responsabilidade. Segue o código que atende ao PRU:</p>

```Java
public class PdfPrinter {

    // Método responsável por imprimir PDF na tela
    public void printPDF( PDDocument document, int page) {
        System.out.println("Inicio");
        PDFTextStripper pdfStripper = null;
        COSDocument cosDoc = null;
        try {
            PDFParser parser = new PDFParser(new FileInputStream(file));
            parser.parse();
            cosDoc = parser.getDocument();
            pdfStripper = new PDFTextStripper();
            document = new PDDocument(cosDoc);
            //Começa a leitura do arquivo a partir da página informada
            pdfStripper.setStartPage(page);

            pdfStripper.setEndPage(pdfStripper.getEndPage());
            String parsedText = pdfStripper.getText(document);

            Scanner s = new Scanner(parsedText);
            s.useDelimiter("\n");

            String linha = "";
            while (s.hasNext()) {
                linha = s.next();
                System.out.println(linha);
            }
            s.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        System.out.println("Fim");

    }
```

<br>

~~~Java
public class PdfModifier {
    // Método responsável por adicionar uma nova string no pdf em nova página
    public PDFDocument addOnPDF(PDDocument document, String string) {
        PDPage page = new PDPage();
        document.addPage(page);
        PDFont font = PDType1Font.COURIER_BOLD;
        PDPageContentStream contentStream = new PDPageContentStream(document, page);
        contentStream.beginText();
        contentStream.setFont(font, 20);
        contentStream.moveTextPositionByAmount(100, 200);
        contentStream.drawString(string); //adiciona a string ao pdf
        contentStream.endText();
        contentStream.close();

        document.save();
        System.out.print("O arquivo foi gerado com sucesso.");
        document.close();
    }

    // Método responsável por substituir uma string no PDF
    public PDDocument replaceOnPdf(PDDocument document, String searchString, String replacement) throws IOException {
        //implementação da pesquisa e substituição de strings no documento pdf
        document.save();
        System.out.print("A string foi substituida");
        document.close();
        return document;
    }
    
    // Método responsável por remover uma string no PDF
    public PDDocument removeOnPdf(PDDocument document, String string) throws IOException {
        //implementação da localização e remoção da string no documento pdf
        document.save();
        System.out.print("A string foi substituida");
        document.close();
        return document;
    }
}
~~~
<p>Crinado a classe <i>PdfPrinter</i> atende-se ao PRU, já que a única responsabilidade da classe <i>PdfPrinter</i> é imprimir um arquivo PDF e a única responsabilidade da classe <i>PdfModifier</i> é modificar um arquivo PDF. Desta forma, com as responsabilidades do código melhor delimitadas, facilitará futuras evoluções e facilitará a compreensão da estrutura e funcionamento da aplicação.</p>

### Conclusão

<p>No exemplo ilustrado era fácil e intuitivo adequar o código ao PRU, porém muitas vezes o critério de responsabilidade usado para encapsular o código pode não ser tão simples de se identificar. Vale ressaltar que o PRU deve ser usado com coesão e responsabilidade, a decisão de dividir uma classe/módulo de um código deve ser cuidadosamente pensada, sempre lembrando que deve-se evitar o design especulativo. Portanto é necessário conhecimento do domínio da problemática para conseguir tomar a melhor decisão possível.</p>

#### Referências
<p>Programação Orientada a Objetos Avançada - Daniel Lucrédio: https://www.youtube.com/watch?v=gbgV5jKZfTk&list=PLaPmgS59eMSFYb42BcmYzVcClCh0t-26L<br>
O que é SOLID - João Roberto da Paixão: https://medium.com/desenvolvendo-com-paixao/o-que-%C3%A9-solid-o-guia-completo-para-voc%C3%AA-entender-os-5-princ%C3%ADpios-da-poo-2b937b3fc530<br>
Single Responsibility Principle in Java: https://www.baeldung.com/java-single-responsibility-principle<br>
Single Responsibility Principle - Halil Özel: https://halilozel1903.medium.com/single-responsibility-principle-3cc51e8f526c<br>
PDFBox - Creating a PDF Document: https://www.tutorialspoint.com/pdfbox/pdfbox_creating_a_pdf_document.htm</p>
