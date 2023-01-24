# CONNECTION_STRING

Criei esse repository com o objetivo de apresentar como fazer uma Connection String utilizando o SQL Server, mas antes vamos conceituar o que é Connection String, ou seja, a Connection String define como vamos nos conectar ao banco de dados, qual usuário e banco vamos utilizar e outros recursos de segurança.

## ***Connection String* (**String de conecção)

A **Connection String** define como vamos nos conectar ao banco de dados, qual usuário e banco vamos utilizar e outros recursos de segurança.

Embora o .NET não tenha uma biblioteca para acesso à dados já incluso, a Microsoft disponibiliza o **Microsoft.Data.SqlClient**, um pacote oficial para conexão e operações no **SQL Server**.

Para adicionar este pacote, basta executar o comando abaixo no Terminal:

```csharp
**dotnet add package Microsoft.Data.SqlClient**
```

Este pacote é a base para conexões ao **SQL Server**, e tanto no Dapper quanto Entity Framework Core, precisamos dele. No caso do Entity Framework Core, ele é instalado automaticamente junto a outro pacote que vou mostrar mais adiante.

Para remover o pacote: dotnet remove package Microsoft.Data.SqlClient

O using `System.Data.SqlClient` nos dará acesso as classes de acesso a dados usadas no projeto : *`SqlConnection, SqlCommand`, `SqlDataReader`* e a propriedade *`ConnectionState`;*

```csharp
using Microsoft.Data.SqlClient; <-----Connection String

namespace BaltaDataAccesse;

public class Program
{
    public static void Main(string[] args)
    {
        const string CONNECTION_STRING = "colocar  URL que se conectará";//string de conecção
        
    }
}
```

## 2. ***SqlConnection***

<aside>
💡 [https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqlconnection?view=dotnet-plat-ext-6.0](https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqlconnection?view=dotnet-plat-ext-6.0)

</aside>

Um `SqlConnection` objeto representa uma sessão exclusiva para uma fonte de dados SQL Server. Com um sistema de banco de dados cliente/servidor, ele é equivalente a uma conexão de rede com o servidor. `SqlConnection` é usado junto com `SqlDataAdapter`  e `SqlCommand` para aumentar o desempenho ao se conectar a um banco de dados Microsoft SQL Server.

Quando você cria uma instância de `SqlConnection` , todas as propriedades são definidas como seus valores iniciais. Para obter uma lista desses valores, consulte o `SqlConnection` construtor.

```csharp
var connection = new SqlConnection();
        connection.Open(); //abrir a conexão

        //INSERT
        //UPDATE

        connection.Close();/*se for fechar conecção e depois utilizar novamente 
                            - OBS: é importante sempre fechar a conexão.*/

        connection.Dispose(); /*quando for fechar uma conexão e não irá abrir mais
         *se vc usar o dispose terá que estanciar o objeto novamente (new), ou seja,
         o connection.open() não funcionará*/
```

Se o `SqlConnection`  escopo ficar fora do escopo, ele não será fechado. Portanto, você deve fechar explicitamente a conexão chamando `Close` ou `Dispose`. `Close` e `Dispose` são funcionalmente equivalentes.

<aside>
💡 Outra forma de se conectar mais rápido ao servidor é utilizando o `using.`

</aside>

Para garantir que as conexões estejam sempre fechadas, abra a conexão dentro de um `using` bloco, conforme mostrado no fragmento de código a seguir. Isso garante que a conexão seja fechada automaticamente quando o código sair do bloco.

```csharp
using Microsoft.Data.SqlClient;

namespace BaltaDataAccesse;

public class Program
{
    public static void Main(string[] args)
    {
        const string connectionString = "Server=localhost";//string de conecção
        
        using (var connection = new SqlConnection(connectionString))
        {
            Console.WriteLine("Conectado");
        }
        
    }
}
```

## 3. ***SqlCommand***

[Conhecendo melhor a classe SqlCommand do .NET](https://www.devmedia.com.br/conhecendo-melhor-a-classe-sqlcommand-do-net/25976)

Entre as classes mais utilizadas do .NET Framework encontra-se a SqlCommand, presente no namespace System.Data.SqlClient. Esta classe é responsável por executar instruções SQL em bancos de dados SQL Server, podendo ser usada tanto para consultas como para instruções “não query”, como updates, inserts e execução de procedures.

### **Principais propriedades**

**CommandText**: essa propriedade do tipo string representa a instrução SQL a ser executada, como um SELECT, UPDATE, INSERT ou o nome de um stored procedure existente.

**CommandTimeout**: trata-se de um valor inteiro que representa o tempo máximo, em segundos, que o SqlCommand deve levar para executar uma instrução. Caso nenhum valor seja definido, o padrão assumido é de 30 segundos.

**CommandType**: a propriedade CommandType define o tipo de instrução a ser executada pelo SqlCommand. Os valores possíveis para essa propriedade vêm de uma enumeração chamada CommandType que possui os seguintes valores.

- **Text**: para instruções DML comuns como selects e inserts. Este é o valor padrão assumido.
- **TableDirect**: caso seja usada, a propriedade CommandText deve contar apenas o nome de uma tabela. Quando o comando é executado, todas as linhas e colunas dessa tabela são retornadas.
- **StoredProcedure**: usado para executar stored procedures. Nesse caso o CommandText deve apenas ser o nome do procedimento.

**Connection**: um objeto do tipo SqlConnection (classe contida no mesmo namespace), que representa e possui os dados da conexão atual com o banco de dados.

**Parameters**: esta é uma coleção de objetos do tipo SqlParameter e, assim como a maioria das coleções, possui métodos como Add, Remove, etc. Os parâmetros dessa lista devem corresponder àqueles definidos no CommandText (identificados por @ antes do nome). Mais adiante teremos exemplos de uso dessa propriedade.

```csharp
using Microsoft.Data.SqlClient;

namespace BaltaDataAccesse;

public class Program
{
    public static void Main(string[] args)
    {
        const string connectionString = "Server=localhost";//string de conecção
        
        using (var connection = new SqlConnection(connectionString))
        {
            Console.WriteLine("Conectado");
            
            using(var command = new SqlCommand())
            {
                command.Connection = connection;
                command.CommandType = System.Data.CommandType.Text;
                command.CommandText = "SELECT [Id], [Title], FRON [Category]";

                var reader = command.ExecuteReader();
                while (reader.Read())
                {
                    Console.WriteLine($"{reader.GetGuid(0)} - {reader.GetString(1)}");
                }
            }
        }
        
    }
}
```

## 4. ***SqlDataReader***

As principais caracteristicas do DataReader são: foward-only (somente avança) e read-only (somente leitura).

Fornece uma maneira de ler um fluxo somente de encaminhamento de linhas com base em um banco de dados SQL Server.

```csharp
var reader = command.ExecuteReader();
                while (reader.Read())
                {
                    Console.WriteLine($"{reader.GetGuid(0)} - {reader.GetString(1)}");
                }
```

Para criar um [SqlDataReader](https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqldatareader?view=dotnet-plat-ext-7.0), você deve chamar o [ExecuteReader](https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqlcommand.executereader?view=dotnet-plat-ext-7.0) método do [SqlCommand](https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqlcommand?view=dotnet-plat-ext-7.0) objeto, em vez de usar diretamente um construtor.

Enquanto o [SqlDataReader](https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqldatareader?view=dotnet-plat-ext-7.0) está sendo usado, o associado [SqlConnection](https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqlconnection?view=dotnet-plat-ext-7.0) está ocupado servindo o [SqlDataReader](https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqldatareader?view=dotnet-plat-ext-7.0), e nenhuma outra operação pode ser executada além de [SqlConnection](https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqlconnection?view=dotnet-plat-ext-7.0) fechá-la. Esse é o caso até que o [Close](https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqldatareader.close?view=dotnet-plat-ext-7.0) método do [SqlDataReader](https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqldatareader?view=dotnet-plat-ext-7.0) seja chamado. Por exemplo, você não pode recuperar parâmetros de saída até depois de chamar [Close](https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqldatareader.close?view=dotnet-plat-ext-7.0).

As alterações feitas em um conjunto de resultados por outro processo ou thread enquanto dados estão sendo lidos podem ser visíveis para o usuário do `SqlDataReader`. No entanto, o comportamento preciso depende do tempo.

<aside>
💡 Para um desempenho ideal, **[SqlDataReader](https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqldatareader?view=dotnet-plat-ext-7.0)** evita criar objetos desnecessários ou fazer cópias desnecessárias de dados. Portanto, várias chamadas para métodos como **[GetValue](https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqldatareader.getvalue?view=dotnet-plat-ext-7.0)**
 retornar uma referência ao mesmo objeto. Tenha cuidado se você estiver modificando o valor subjacente dos objetos retornados por métodos como **[GetValue](https://learn.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqldatareader.getvalue?view=dotnet-plat-ext-7.0)**.

</aside>

Em meus estudos encontrei vários tipos de conexão, seja com a criação de uma classe ou utilizando o using, cujo achei o método mais simples e prático.

______________________________________________________________
1. Método utilizando Classe:


                using Microsoft.Data.SqlClient;

                namespace Cadastro
                {
                    public class Conexao
                    {
                        SqlConnection conexao = new SqlConnection();
                        
                        public Conexao()
                        {
                            conexao.ConnectionString = 
                            @"Password= colcar senha;
                            Persist Security Info= True;
                            User ID=sa (valor padrão);
                            Database= tabela;
                            Server= nome do Banco de dados que quer acessar";
                        }
                        
                        public SqlConnection Conectar()
                        {
                            if (conexao.State == System.Data.ConnectionState.Closed)
                            {
                                conexao.Open();
                            }
                            return conexao;
                        }
                        
                        public void Desconectra()
                        {
                            if (conexao.State == System.Data.ConnectionState.Open)
                            {
                                conexao.Close();
                            }
                        }
                    }
                }


Quando você cria uma instância de SqlConnection, todas as propriedades são definidas como seus valores iniciais. Para obter uma lista desses valores, consulte o SqlConnection construtor.

                    var connection = new SqlConnection();
                    
                    connection.Open(); //abrir a conexão
                    //INSERT
                    //UPDATE
                    
                    connection.Close();/*se for fechar conecção e depois utilizar novamente 
                                        - OBS: é importante sempre fechar a conexão.*/
                    
                    connection.Dispose(); /*quando for fechar uma conexão e não irá abrir mais,
                     se vc usar o dispose terá que estanciar o objeto novamente (new), ou seja,
                     o connection.open() não funcionará*/

Se o SqlConnection ficar fora do escopo, ele não será fechado. Portanto, você deve fechar explicitamente a conexão chamando Close ou Dispose. Close e Dispose são funcionalmente equivalentes.

______________________________________________________________
2. Método utilizando using:

Para garantir que as conexões estejam sempre fechadas, abra a conexão dentro de um using bloco, conforme mostrado no fragmento de código a seguir. Isso garante que a conexão seja fechada automaticamente quando o código sair do bloco.

                    using Microsoft.Data.SqlClient;

                    namespace BaltaDataAccesse;

                    public class Program
                    {
                        public static void Main(string[] args)
                        {
                            const string CONNECTION_STRING = "Server=localhost...";//string de conecção
                           
                            using (var connection = new SqlConnection(CONNECTION_STRING))
                            {
                                Console.WriteLine("Conectado");
                            }

                        }
                    }

Podemos verificar que foi criado uma constante para interligar a connection string, assim não precisamos criar uma classe para a conexão, em seguida foi utilizado o using concatenando com a constante criada, desta forma não precisamos ficar abrindo e fechando a conexão, ou seja, utilizar o connection.Open(), connection.Close() e connection.Dispose() para a utilização do CRUD.

Link mostrando como conseguir de froma mais rapida a string connection:  https://www.youtube.com/watch?v=yvNW8oEKQ7Q
