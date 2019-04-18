
# TypeScript / React Presentation

## Example Entity

```TypeScript
@Entity()
export class Content {
  @PrimaryGeneratedColumn("increment")
  public id?: number;

  public nodeId?: string;

  @Column({ length: 32, comment: "Identifies how content should behave." })
  public tag: string;

  @Column({ length: 128, comment: "Title of the content." })
  public title: string;

  @Column({ length: 512, comment: "Thumbnail representing the content." })
  public image: string;

  @Column({ length: 512, comment: "Short description of content." })
  public description: string;

  @Column("jsonb", { comment: "@jsonField children array \n Therapy content.",
                     default: `{"children":[]}`, nullable: false })
  public props?: IProps;

  @Column("timestamp without time zone", { default: () => "(NOW() AT TIME ZONE 'UTC')"})
  public createdAt?: Date;

  @Column("timestamp without time zone", { default: () => "(NOW() AT TIME ZONE 'UTC')"})
  public updatedAt?: Date;
}
```

## Example Migration

```TypeScript
import {MigrationInterface, QueryRunner} from "typeorm";

export class Therapy1539901776490 implements MigrationInterface {

    public async up(queryRunner: QueryRunner): Promise<any> {
        await queryRunner.query(`CREATE TABLE "content" ("id" SERIAL NOT NULL, "tag" character varying(32) NOT NULL, "title" character varying(128) NOT NULL, "image" character varying(512) NOT NULL, "description" character varying(512) NOT NULL, "props" jsonb NOT NULL DEFAULT '{"children":[]}', "createdAt" TIMESTAMP NOT NULL DEFAULT (NOW() AT TIME ZONE 'UTC'), "updatedAt" TIMESTAMP NOT NULL DEFAULT (NOW() AT TIME ZONE 'UTC'), CONSTRAINT "PK_6a2083913f3647b44f205204e36" PRIMARY KEY ("id"))`);
    }

    public async down(queryRunner: QueryRunner): Promise<any> {
        await queryRunner.query(`DROP TABLE "content"`);
    }

}
```

## Example graphql

```TypeScript
export const UPDATE_CONTENT = gql`
mutation UPDATE_CONTENT($id: Int!, $tag: String!, $title: String!,
 $image: String!, $description: String!, $props: JSON!) {
    updateContentById(input: {id:$id, contentPatch: { tag:$tag,
    title:$title, image:$image, description:$description,
    props:$props}}) {
        content {
            __typename
            nodeId
            id
            tag
            title
            image
            description
            props
        }
    }
}
`;

export const QUERY_CONTENT = gql`
query QUERY_CONTENT($id: Int!) {
    contentById(id:$id) {
        __typename
        nodeId
        id
        tag
        title
        image
        description
        props
    }
}
`;
```

## Example `graphql-code-generator`

```javascript
const generate = require('graphql-code-generator').generate;

async function doSomething() {
    const generatedFiles = await generate({
        args: ['./src/graphql/**/*.ts', './src/containers/**/*/query.gql.ts'],
        clientSchema: './src/containers/**/*/query.graphql',
        overwrite: true,
        template: 'graphql-codegen-typescript-template',
        schema: 'http://localhost:3000/graphql',
        skipSchema: true,
        out: process.cwd() + '/src/models/'
    }, true);
}

doSomething();
```

```TypeScript
export namespace UpdateContent {
  export type Variables = {
    id: number;
    tag: string;
    title: string;
    image: string;
    description: string;
    props: Json;
  };

  export type Mutation = {
    __typename?: "Mutation";
    updateContentById?: UpdateContentById | null;
  };

  export type UpdateContentById = {
    __typename?: "UpdateContentPayload";
    content?: Content | null;
  };

  export type Content = {
    __typename?: "Content";
    nodeId: string;
    id: number;
    tag: string;
    title: string;
    image: string;
    description: string;
    props: Json;
  };
}
```

## Example Hook

```TypeScript
export const useTherapy = (therapyId: number) => {
    const { data, errors, loading } =
        useQuery<QueryTherapy.Query, QueryTherapy.Variables>(QUERY_THERAPY, {
        suspend: false,
        variables: { id: therapyId },
    });
    return {
        errors,
        loading,
        therapy: data && data.therapyById ? data.therapyById : null,
    };
};

export const useUpdateTherapy = () => {
    return useMutation<UpdateTherapy.Mutation, UpdateTherapy.Variables>(UPDATE_THERAPY);
};
```
