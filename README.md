# RPGGame

## Descrição do Projeto

Este projeto envolve um sistema de criação e gerenciamento de personagens para um jogo RPG, com dados armazenados em um banco de dados MySQL e interação feita em Java.

## MySQL

### Estrutura do Banco de Dados

Execute o seguinte script SQL para criar a tabela `pessoa`:

```sql
CREATE TABLE pessoa (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100),
    sexo ENUM('Masculino', 'Feminino'),
    raca VARCHAR(50),
    onde_nasceu VARCHAR(50),
    onde_mora VARCHAR(100),
    o_que_faz VARCHAR(100),
    qual_sua_classe VARCHAR(30),
    habilidades TEXT
);

INSERT INTO pessoa (nome, sexo, raca, onde_nasceu, onde_mora, o_que_faz, qual_sua_classe, habilidades)
VALUES ('dick', 'Masculino', 'humano com elfo', 'nasceu na floresta negra', 'mora na cidade de tempeste', 'é caçador', 'necromante e assassino', 
'invoçcação, controle de elementos, controle das sombras, furtividade e controle mental');
```

## Java

### Conexão com o Banco de Dados

```java
package com.vini.main;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;
import java.util.logging.Level;
import java.util.logging.Logger;

public class ConexaoMySQL {

    final String url = "jdbc:mysql://localhost:3306/bdteste";
    final String usuario = "root";
    final String senha = "root";

    Connection conexao = null;
    PreparedStatement pstmt = null;
    Statement stmt = null;
    ResultSet rs = null;
    Scanner teclado = new Scanner(System.in);

    public ConexaoMySQL() {
        try {
            System.out.println("Tentando conectar ao banco de dados...");
            conexao = DriverManager.getConnection(url, usuario, senha);
            System.out.println("Conexão foi estabelecida com sucesso!");

            System.out.println("Digite os seguintes dados:");

            String nome = GetNome();
            String sexo = GetGenero();
            String qual_sua_raca = GetRaça();
            String onde_nasceu = GetOnde_nasceu();
            String onde_mora = GetOnde_mora();
            String o_que_faz = GetO_que_faz();
            String qual_sua_classe = GetCalsse();
            String habilidades_1 = GetHabilidade_1();
            String habilidades_2 = GetHabilidade_2();
            String habilidades_3 = GetHabilidade_3();
            String habilidades_4 = GetHabilidade_4();
            String habilidades_5 = GetHabilidade_5();

            String sqlInsert = "INSERT INTO pessoa (nome, sexo, qual_sua_raca, onde_nasceu, onde_mora, o_que_faz, qual_sua_classe, habilidades_1, habilidades_2, habilidades_3, habilidades_4, habilidades_5) VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)";

            pstmt = conexao.prepareStatement(sqlInsert);
            pstmt.setString(1, nome);
            pstmt.setString(2, sexo);
            pstmt.setString(3, qual_sua_raca);
            pstmt.setString(4, onde_nasceu);
            pstmt.setString(5, onde_mora);
            pstmt.setString(6, o_que_faz);
            pstmt.setString(7, qual_sua_classe);
            pstmt.setString(8, habilidades_1);
            pstmt.setString(9, habilidades_2);
            pstmt.setString(10, habilidades_3);
            pstmt.setString(11, habilidades_4);
            pstmt.setString(12, habilidades_5);
            pstmt.executeUpdate();

            String sqlSelect = "SELECT * FROM pessoa";
            stmt = conexao.createStatement();
            rs = stmt.executeQuery(sqlSelect);
            System.out.println("Consulta executada com sucesso!");

            while (rs.next()) {
                int id = rs.getInt("id");
                String nomeDb = rs.getString("nome");
                String sexoDb = rs.getString("sexo");
                String qual_sua_racaDb = rs.getString("qual_sua_raca");
                String onde_nasceuDb = rs.getString("onde_nasceu");
                String onde_moraDb = rs.getString("onde_mora");
                String o_que_fazDb = rs.getString("o_que_faz");
                String qual_sua_classeDb = rs.getString("qual_sua_classe");
                String habilidades_1Db = rs.getString("habilidades_1");
                String habilidades_2Db = rs.getString("habilidades_2");
                String habilidades_3Db = rs.getString("habilidades_3");
                String habilidades_4Db = rs.getString("habilidades_4");
                String habilidades_5Db = rs.getString("habilidades_5");

                System.out.println("\nID: " + id
                        + "\nNome: " + nomeDb
                        + "\nGenero: " + sexoDb
                        + "\nRaça: " + qual_sua_racaDb
                        + "\nOnde Nasceu: " + onde_nasceuDb
                        + "\nOnde Mora: " + onde_moraDb
                        + "\nO que Faz: " + o_que_fazDb
                        + "\nClasse: " + qual_sua_classeDb
                        + "\nHabilidade 1: " + habilidades_1Db
                        + "\nHabilidade 2: " + habilidades_2Db
                        + "\nHabilidade 3: " + habilidades_3Db
                        + "\nHabilidade 4: " + habilidades_4Db
                        + "\nHabilidade 5: " + habilidades_5Db);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            try {
                if (rs != null) {
                    rs.close();
                }
                if (stmt != null) {
                    stmt.close();
                }
                if (pstmt != null) {
                    pstmt.close();
                }
                if (conexao != null) {
                    conexao.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        new ConexaoMySQL();
    }

    private String GetNome() {
        System.out.println("Nome:");
        return teclado.nextLine();
    }

    private String GetGenero() {
        List<Genero> genero = GetGeneros();
        while (true) {
            for (int i = 0; i < genero.size(); i++) {
                Genero g = genero.get(i);
                String nome = g.nome;
                System.out.println((i + 1) + " - " + nome);
            }
            System.out.println("Digite o número da  opção desejada:");
            Scanner sc = new Scanner(System.in);
            int opcao = sc.nextInt();
            if (0 < opcao && opcao <= genero.size()) {
                return genero.get(opcao - 1).nome;
            } else {
                System.out.println("Digite uma opcao valida\n");
            }
        }
    }

    private List<Genero> GetGeneros() {
        List<Genero> genero = new ArrayList<>();
        try (Connection cn = DriverManager.getConnection(url, usuario, senha)) {
            Statement s = cn.createStatement();
            ResultSet rs = s.executeQuery("SELECT * FROM generos");
            while (rs.next()) {
                int id = rs.getInt("id");
                String nome = rs.getString("nome");
                Genero g = new Genero(id, nome);
                genero.add(g);
            }
        } catch (SQLException ex) {
            Logger.getLogger(ConexaoMySQL.class.getName()).log(Level.SEVERE, null, ex);
        }
        return genero;
    }

    private String GetRaça() {
        System.out.println("Raça:");
        return teclado.nextLine();
    }

    private String GetOnde_nasceu() {
        System.out.println("Onde nasceu:");
        return teclado.nextLine();
    }

    private String GetOnde_mora() {
        System.out.println("Onde mora:");
        return teclado.nextLine();
    }

    private String GetO_que_faz() {
        System.out.println("O que faz:");
        return teclado.nextLine();
    }
}
```

### Estrutura do Projeto

```
JogoRPG/
│
├── ConexaoMySQL.java
```

## Contribuições

Contribuições são bem-vindas! Sinta-se à vontade para abrir issues e pull requests.

## Licença

Este projeto está licenciado sob a MIT License.

## Contato

Autor: 
Email: 
