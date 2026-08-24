import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.*;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.List;

public class GerenciadorDeLogs {

    private static final Path PASTA_APP =
            Paths.get(System.getProperty("user.home"), "MeuAplicativo");

    private static final Path ARQUIVO_LOG =
            PASTA_APP.resolve("app.log");

    private static final DateTimeFormatter FORMATO =
            DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");

    // Cria a pasta e o arquivo de log do próprio aplicativo
    public static void iniciar() {
        try {
            Files.createDirectories(PASTA_APP);

            if (!Files.exists(ARQUIVO_LOG)) {
                Files.createFile(ARQUIVO_LOG);
            }

            registrar("Sistema de logs iniciado.");
        } catch (IOException e) {
            System.err.println("Erro ao iniciar logs: " + e.getMessage());
        }
    }

    // Registra um evento do próprio aplicativo
    public static void registrar(String mensagem) {
        try {
            String data = LocalDateTime.now().format(FORMATO);
            String linha = "[" + data + "] " + mensagem + System.lineSeparator();

            Files.writeString(
                    ARQUIVO_LOG,
                    linha,
                    StandardCharsets.UTF_8,
                    StandardOpenOption.CREATE,
                    StandardOpenOption.APPEND
            );

        } catch (IOException e) {
            System.err.println("Erro ao registrar log: " + e.getMessage());
        }
    }

    // Mostra os logs
    public static void visualizar() {
        try {
            if (!Files.exists(ARQUIVO_LOG)) {
                System.out.println("Nenhum log encontrado.");
                return;
            }

            List<String> linhas = Files.readAllLines(
                    ARQUIVO_LOG,
                    StandardCharsets.UTF_8
            );

            System.out.println("\n===== LOGS DO APLICATIVO =====");

            if (linhas.isEmpty()) {
                System.out.println("Nenhum registro.");
            } else {
                linhas.forEach(System.out::println);
            }

            System.out.println("==============================\n");

        } catch (IOException e) {
            System.err.println("Erro ao ler logs: " + e.getMessage());
        }
    }

    // Limpa SOMENTE o arquivo de log deste aplicativo
    public static void limparLogsDoAplicativo() {
        try {
            Files.writeString(
                    ARQUIVO_LOG,
                    "",
                    StandardCharsets.UTF_8,
                    StandardOpenOption.CREATE,
                    StandardOpenOption.TRUNCATE_EXISTING
            );

            System.out.println("Logs do aplicativo foram limpos.");

        } catch (IOException e) {
            System.err.println("Erro ao limpar logs: " + e.getMessage());
        }
    }

    public static void main(String[] args) {

        iniciar();

        registrar("Aplicativo aberto.");
        registrar("Usuário iniciou uma sessão.");

        visualizar();

        // Descomente para limpar os logs DESTE aplicativo:
        // limparLogsDoAplicativo();
    }
}
