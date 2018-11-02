# load-proto

Load proto file from gitlab, and convert to protobufjs' [Root](http://dcode.io/protobuf.js/Root.html)

## Example

```typescript
import { loadProto } from "load-proto";

const token = process.argv[2];

loadProto({
  gitUrls: [
    {
      url: 'git@git.myscrm.cn:ykcommon/ykproto.git',
      branch: 'master',
    },
    'git@git.myscrm.cn:2c/panther-proto.git',
    'git@git.myscrm.cn:2c/thanos-third-proto.git',
  ],
  accessToken: token,
  branch: 'dev',
  resolvePath: (origin: string, target: string, rootDir: string) => {
    if (/^git\.myscrm\.cn\/golang\/common\/proto\/(google|common)\//.test(target)) {
      return `${rootDir}/git.myscrm.cn/ykcommon/ykproto/${target.replace(/^git\.myscrm\.cn\/golang\/common\/proto\//, '')}`;
    } else if (/^proto\/(common|google)\//.test(target)) {
      return `${rootDir}/git.myscrm.cn/ykcommon/ykproto/${target.replace(/^proto\//, '')}`;
    } else if (/^proto\//.test(target)) {
      return target.replace(/^proto\/([^\/]+)(.+)/, (_target, $1, $2) => {
        return `${rootDir}/git.myscrm.cn/2c/${$1.replace(/_/g, '-')}${$2}`;
      });
    } else if (/^(common|google\/api)\//.test(target)) {
      return `${rootDir}/git.myscrm.cn/ykcommon/ykproto/${target}`;
    }
    return null;
  },
})
  .then((root) => {
    console.info(root);
  })
```