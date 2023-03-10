# react-Apollo+Codegen+TypeScript，如何将多个查询/变异组合到一个组件中

> 原文：<https://dev.to/piglovesyou/react-apollo-codegen-typescript-how-you-can-compose-multiple-queries-mutations-to-a-component-2jic>

Apollo 团队已经提供了[像样的文档](https://www.apollographql.com/docs/react/recipes/static-typing),允许你用 Codegen 生成的类型来键入你的组件。

这是一个快速教程，可以让您更深入地编写多个 GraphQL 查询/变异来绑定到单个组件。

# 总结

*   从外部 hoc(例如`withData`函数)到内部 hoc 的定义
*   使用-data-props 传递给子 HOC
*   不要使用`compose()`功能

# 举例

假设这是一个新的学生创建表单，需要下拉学校列表。学校通过 GraphQL 查询进行解析。同样，表单提交是通过 GraphQL 变异完成的。

```
// UserCreate.tsx

import {ChildDataProps, ChildMutateProps} from 'react-apollo';
import {
  CreateUserMutation,
  CreateUserMutationVariables,
} from './__generated__/CreateUserMutation';
import { SchoolsQuery } from './__generated__/SchoolsQuery';
import { CreateUserInput } from "../../../../__generated__/globalTypes";

type ComponentProps = { title: string, };
type WithDataProps = ChildDataProps<ComponentProps, SchoolsQuery, {}>;
const withData = graphql<ComponentProps, SchoolsQuery, {}, WithDataProps>(gql`
  query SchoolsQuery {
    schools {
      id
      email
      nameJa
      nameEn
      phone
    }
  }
`);

type WithMutateProps = ChildMutateProps<WithDataProps, CreateUserMutation, CreateUserMutationVariables>;
const withMutate = graphql<WithDataProps, CreateUserMutation, CreateUserMutationVariables, WithMutateProps>(gql`
  mutation CreateUserMutation($input: CreateUserInput!) {
    createUser(input: $input) {
      email
      nameEn
      nameJa
      school
    }
  }
`);

const UserCreate = withData(withMutate(props => {
  const {
    mutate: createUser,
    data: {schools},
  } = props;
  return (
    <Container>
      <h1>Create New User</h1>
      <Form
        method="post"
        onSubmit={async (e: any) => {
          e.preventDefault();

          const input = (createFormData(e.target) as CreateUserInput ) ;

          const result = await createUser({
            refetchQueries: [
              ({query: UsersQueryGQL} as PureQueryOptions)
            ],
            variables: {input},
          });
          if (!result || !result.data) throw new Error('Mutation failed.');

          const {
            data: {createUser: created},
          } = result;

          // Do something with created user data

          history.push('/users');
        }}
      >
        <FormGroup>
          <Label for="email">User email address:</Label>
          <Input id="email" type="text" name="email"/>
        </FormGroup> 
        <FormGroup>
          <Label for="nameJa">User name (Japanese):</Label>
          <Input id="nameJa" type="text" name="nameJa"/>
        </FormGroup> 
        <FormGroup>
          <Label for="nameEn">User name (English):</Label>
          <Input id="nameEn" type="text" name="nameEn"/>
        </FormGroup> 
        {schools && (
          <>
            <FormGroup>
              <Label for="school">school</Label>
              <p>
                <select id="school" name="school">
                  <option>---please select---</option>
                  {schools.map(s => (
                    <option key={s.id} value={s.id}>
                      {s.nameEn}
                    </option>
                  ))}
                </select>
              </p>
            </FormGroup>
          </>
        )}

        <FormGroup>
          <Button color="primary" type="submit" block>
            Log in
          </Button>
        </FormGroup>
      </Form>
    </Container>
  );
}));

export default UserCreate; 
```

# 在外 HOC

```
type WithDataProps = ChildDataProps<ComponentProps, SchoolsQuery, {}>;
const withData = graphql<ComponentProps, SchoolsQuery, {}, WithDataProps>(/*...*/); 
```

*   `graphql()`的通用参数中的第一个参数被用作实际可接受的属性
*   `WithDataProps`被用作我们传递给下一个内部组件的类型
    *   所以这是我们内心`withMutate`应该接受的。

# 在内心自居

```
type WithMutateProps = ChildMutateProps<WithDataProps, CreateUserMutation, CreateUserMutationVariables>;
const withMutate = graphql<WithDataProps, CreateUserMutation, CreateUserMutationVariables, WithMutateProps> 
```

*   接受道具`WithDataProps`，从外链`withData`传来
    *   其中已经包含了`ComponentProps`，所以组件消耗的最终属性是 3 个的复合:查询数据类型、突变类型和组件自身属性。

# `compose`功能？

截至 react-apollo 版本`2.5.2`，`compose`未输入。

```
export declare function compose(...funcs: Function[]): (...args: any[]) => any; 
```

我写这篇文章是因为我找不到这样做的例子。希望对某人有帮助。