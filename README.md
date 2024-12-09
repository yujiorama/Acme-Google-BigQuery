[![Actions Status](https://github.com/yujiorama/Google-BigQuery-Lite/actions/workflows/test.yml/badge.svg)](https://github.com/yujiorama/Google-BigQuery-Lite/actions)
# NAME

Google::BigQuery::Lite - Client of Google BigQuery REST API (v2) only jobs.query

# SYNOPSIS

    use Google::BigQuery::Lite;

    # create a instance
    my $bq_lite = Google::BigQuery::Lite->new(
        client_email     => $client_email,
        private_key_file => $private_key_file,
        project_id       => $project_id,
    );
    # set  default dataset
    my $dataset_id = 'usa_names';
    $bq_lite->use_dataset($dataset_id);

    # define field name and json schema
    my $field_definitions = [
        { name => 'id', schema => +{
            type => ['integer'],
        } },
        { name => 'int_value', schema => +{
            type => ['integer'],
        } },
        { name => 'num_value', schema => +{
            type    => ['number'],
            minimum => 0.0,
            maximum => 100.0,
        } },
        { name => 'array_int', schema => +{
            type     => [ 'array', 'null' ],
            maxItems => 10,
            items    => +{
                type => 'integer',
            }
        } },
    ];

    my $row = $bq_lite->selectrow_arrayref(
        query => "SELECT * FROM $table_id ORDER BY id LIMIT 1",
        useLegacySql => 0,
        field_definitions => $field_definitions,
    );
    # { id => 123, int_value => 1234, num_value => 123.4, array_int => [ 1, 12, 123 ] }

    my $rows = $bq_lite->selectall_arrayref(
        query => "SELECT * FROM $table_id ORDER BY id",
        useLegacySql => 0,
        field_definitions => $field_definitions,
    );
    # [{ id => 123, int_value => 1234, num_value => 123.4, array_int => [ 1, 12, 123 ] }]

# DESCRIPTION

[Google::BigQuery::Lite](https://metacpan.org/pod/Google%3A%3ABigQuery%3A%3ALite) は [Google BigQuery REST API (v2) の jobs.query](https://cloud.google.com/bigquery/docs/reference/rest/v2/jobs/query) に限定したクライアントパッケージを提供します。

[Google::BigQuery](https://metacpan.org/pod/Google%3A%3ABigQuery) の `selectall_arrayref` メソッドの代わりに利用してください。

# INSTALL

    cpanm https://github.com/yujiorama/Google-BigQuery-Lite.git

or

    cpm install https://github.com/yujiorama/Google-BigQuery-Lite.git

# METHODS

- `$bq_lite->use_dataset($database_id);`

    インスタンスにデフォルトのデータセットIDを設定します。

- `my $row = $bq_lite->selectrow_arrayref(query => $query, field_definitions => $field_definitions, %args);`

    `$query`を実行して1行の結果を取得します。

    `%args` に指定する値は `QueryRequest|https://cloud.google.com/bigquery/docs/reference/rest/v2/jobs/query#QueryRequest` を参照してください。

    このメソッドは常に `maxResults=1` として実行するため、結果が1行を超えることはありません。

    `$field_definitions` は、 `$query` に指定したGoogle SQLのSELECT句に並べた列と同じ順番に、名前とデータ型を [JSON Schema](https://json-schema.org/specification) で定義します。

        my $field_definitions = [
            # id is not null, must be integer
            { name => 'id', schema => +{
                type => ['integer'],
            } },
            # int_value is not null, must be integer
            { name => 'int_value', schema => +{
                type => ['integer'],
            } },
            # num_value is not null, must be number, minimum 0.0, maximum 100.0
            { name => 'num_value', schema => +{
                type    => ['number'],
                minimum => 0.0,
                maximum => 100.0,
            } },
            # array_int is nullable array of integer, max items 10
            { name => 'array_int', schema => +{
                type     => [ 'array', 'null' ],
                maxItems => 10,
                items    => +{
                    type => 'integer',
                }
            } },
        ];

- `my $rows = $bq_lite->selectrall_arrayref(query => $query, field_definitions => $field_definitions, %args);`

    `$query`を実行してすべての結果を取得します。

    `%args` に指定する値は `QueryRequest|https://cloud.google.com/bigquery/docs/reference/rest/v2/jobs/query#QueryRequest` を参照してください。

    `$field_definitions` は、 `$query` に指定したGoogle SQLのSELECT句に並べた列と同じ順番に、名前とデータ型を [JSON Schema](https://json-schema.org/specification) で定義します。

        my $field_definitions = [
            # id is not null, must be integer
            { name => 'id', schema => +{
                type => ['integer'],
            } },
            # int_value is not null, must be integer
            { name => 'int_value', schema => +{
                type => ['integer'],
            } },
            # num_value is not null, must be number, minimum 0.0, maximum 100.0
            { name => 'num_value', schema => +{
                type    => ['number'],
                minimum => 0.0,
                maximum => 100.0,
            } },
            # array_int is nullable array of integer, max items 10
            { name => 'array_int', schema => +{
                type     => [ 'array', 'null' ],
                maxItems => 10,
                items    => +{
                    type => 'integer',
                }
            } },
        ];

# LICENSE

Copyright (C) Yuji Okazawa.

This library is free software; you can redistribute it and/or modify
it under the same terms as Perl itself.

# AUTHOR

Yuji Okazawa &lt;yujiorama+github@gmail.com>
